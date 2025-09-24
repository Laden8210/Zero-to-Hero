# State Management with APIs

## Introduction
This lesson covers advanced state management techniques when working with APIs, including caching strategies, optimistic updates, error boundaries, and complex state synchronization patterns.

## Advanced State Management Patterns

### API State Management with Context
```javascript
// contexts/ApiContext.js
import React, { createContext, useContext, useReducer, useEffect } from 'react';

const ApiContext = createContext();

// Action types
const API_ACTIONS = {
  FETCH_START: 'FETCH_START',
  FETCH_SUCCESS: 'FETCH_SUCCESS',
  FETCH_ERROR: 'FETCH_ERROR',
  UPDATE_ITEM: 'UPDATE_ITEM',
  DELETE_ITEM: 'DELETE_ITEM',
  ADD_ITEM: 'ADD_ITEM',
  CLEAR_ERROR: 'CLEAR_ERROR',
  SET_CACHE: 'SET_CACHE',
  INVALIDATE_CACHE: 'INVALIDATE_CACHE',
};

// Initial state
const initialState = {
  data: [],
  loading: false,
  error: null,
  cache: new Map(),
  lastFetch: null,
  pagination: {
    page: 1,
    limit: 10,
    total: 0,
    hasMore: false,
  },
};

// Reducer
function apiReducer(state, action) {
  switch (action.type) {
    case API_ACTIONS.FETCH_START:
      return {
        ...state,
        loading: true,
        error: null,
      };
    
    case API_ACTIONS.FETCH_SUCCESS:
      return {
        ...state,
        loading: false,
        data: action.payload.data,
        pagination: action.payload.pagination,
        lastFetch: Date.now(),
        error: null,
      };
    
    case API_ACTIONS.FETCH_ERROR:
      return {
        ...state,
        loading: false,
        error: action.payload,
      };
    
    case API_ACTIONS.UPDATE_ITEM:
      return {
        ...state,
        data: state.data.map(item =>
          item.id === action.payload.id ? { ...item, ...action.payload.updates } : item
        ),
      };
    
    case API_ACTIONS.DELETE_ITEM:
      return {
        ...state,
        data: state.data.filter(item => item.id !== action.payload.id),
      };
    
    case API_ACTIONS.ADD_ITEM:
      return {
        ...state,
        data: [...state.data, action.payload],
      };
    
    case API_ACTIONS.CLEAR_ERROR:
      return {
        ...state,
        error: null,
      };
    
    case API_ACTIONS.SET_CACHE:
      const newCache = new Map(state.cache);
      newCache.set(action.payload.key, {
        data: action.payload.data,
        timestamp: Date.now(),
      });
      return {
        ...state,
        cache: newCache,
      };
    
    case API_ACTIONS.INVALIDATE_CACHE:
      const invalidatedCache = new Map(state.cache);
      invalidatedCache.delete(action.payload.key);
      return {
        ...state,
        cache: invalidatedCache,
      };
    
    default:
      return state;
  }
}

// Provider component
export function ApiProvider({ children, baseUrl }) {
  const [state, dispatch] = useReducer(apiReducer, initialState);

  // Cache management
  const getCachedData = (key) => {
    const cached = state.cache.get(key);
    if (cached && Date.now() - cached.timestamp < 5 * 60 * 1000) { // 5 minutes
      return cached.data;
    }
    return null;
  };

  const setCachedData = (key, data) => {
    dispatch({
      type: API_ACTIONS.SET_CACHE,
      payload: { key, data },
    });
  };

  // API functions
  const fetchData = async (endpoint, options = {}) => {
    const cacheKey = `${endpoint}-${JSON.stringify(options)}`;
    const cachedData = getCachedData(cacheKey);
    
    if (cachedData && !options.forceRefresh) {
      dispatch({
        type: API_ACTIONS.FETCH_SUCCESS,
        payload: { data: cachedData, pagination: {} },
      });
      return cachedData;
    }

    dispatch({ type: API_ACTIONS.FETCH_START });

    try {
      const response = await fetch(`${baseUrl}${endpoint}`, {
        headers: {
          'Content-Type': 'application/json',
          ...options.headers,
        },
        ...options,
      });

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      const result = await response.json();
      
      dispatch({
        type: API_ACTIONS.FETCH_SUCCESS,
        payload: { data: result.data, pagination: result.pagination },
      });

      setCachedData(cacheKey, result.data);
      return result.data;
    } catch (error) {
      dispatch({
        type: API_ACTIONS.FETCH_ERROR,
        payload: error.message,
      });
      throw error;
    }
  };

  const updateItem = async (id, updates) => {
    try {
      const response = await fetch(`${baseUrl}/items/${id}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(updates),
      });

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      const updatedItem = await response.json();
      
      dispatch({
        type: API_ACTIONS.UPDATE_ITEM,
        payload: { id, updates: updatedItem },
      });

      return updatedItem;
    } catch (error) {
      dispatch({
        type: API_ACTIONS.FETCH_ERROR,
        payload: error.message,
      });
      throw error;
    }
  };

  const deleteItem = async (id) => {
    try {
      const response = await fetch(`${baseUrl}/items/${id}`, {
        method: 'DELETE',
      });

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      dispatch({
        type: API_ACTIONS.DELETE_ITEM,
        payload: { id },
      });

      return true;
    } catch (error) {
      dispatch({
        type: API_ACTIONS.FETCH_ERROR,
        payload: error.message,
      });
      throw error;
    }
  };

  const addItem = async (item) => {
    try {
      const response = await fetch(`${baseUrl}/items`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(item),
      });

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      const newItem = await response.json();
      
      dispatch({
        type: API_ACTIONS.ADD_ITEM,
        payload: newItem,
      });

      return newItem;
    } catch (error) {
      dispatch({
        type: API_ACTIONS.FETCH_ERROR,
        payload: error.message,
      });
      throw error;
    }
  };

  const clearError = () => {
    dispatch({ type: API_ACTIONS.CLEAR_ERROR });
  };

  const invalidateCache = (key) => {
    dispatch({
      type: API_ACTIONS.INVALIDATE_CACHE,
      payload: { key },
    });
  };

  const value = {
    ...state,
    fetchData,
    updateItem,
    deleteItem,
    addItem,
    clearError,
    invalidateCache,
  };

  return (
    <ApiContext.Provider value={value}>
      {children}
    </ApiContext.Provider>
  );
}

// Custom hook
export function useApi() {
  const context = useContext(ApiContext);
  if (!context) {
    throw new Error('useApi must be used within an ApiProvider');
  }
  return context;
}
```

## Optimistic Updates

### Optimistic Update Implementation
```javascript
// hooks/useOptimisticUpdates.js
import { useState, useCallback } from 'react';

export function useOptimisticUpdates(initialData = []) {
  const [data, setData] = useState(initialData);
  const [pendingUpdates, setPendingUpdates] = useState(new Map());

  const optimisticUpdate = useCallback(async (id, updates, apiCall) => {
    // Create optimistic update
    const optimisticId = `optimistic-${Date.now()}`;
    const optimisticItem = { id: optimisticId, ...updates, _isOptimistic: true };
    
    // Add to pending updates
    setPendingUpdates(prev => new Map(prev).set(optimisticId, { id, updates, apiCall }));
    
    // Apply optimistic update
    setData(prev => prev.map(item => 
      item.id === id ? { ...item, ...updates, _isOptimistic: true } : item
    ));

    try {
      // Make API call
      const result = await apiCall(id, updates);
      
      // Remove from pending updates
      setPendingUpdates(prev => {
        const newMap = new Map(prev);
        newMap.delete(optimisticId);
        return newMap;
      });
      
      // Apply real update
      setData(prev => prev.map(item => 
        item.id === id ? { ...item, ...result, _isOptimistic: false } : item
      ));
      
      return result;
    } catch (error) {
      // Revert optimistic update
      setPendingUpdates(prev => {
        const newMap = new Map(prev);
        newMap.delete(optimisticId);
        return newMap;
      });
      
      setData(prev => prev.map(item => 
        item.id === id ? { ...item, _isOptimistic: false } : item
      ));
      
      throw error;
    }
  }, []);

  const retryPendingUpdate = useCallback(async (optimisticId) => {
    const pendingUpdate = pendingUpdates.get(optimisticId);
    if (!pendingUpdate) return;

    try {
      const result = await pendingUpdate.apiCall(pendingUpdate.id, pendingUpdate.updates);
      
      setPendingUpdates(prev => {
        const newMap = new Map(prev);
        newMap.delete(optimisticId);
        return newMap;
      });
      
      setData(prev => prev.map(item => 
        item.id === pendingUpdate.id ? { ...item, ...result, _isOptimistic: false } : item
      ));
      
      return result;
    } catch (error) {
      throw error;
    }
  }, [pendingUpdates]);

  const cancelPendingUpdate = useCallback((optimisticId) => {
    setPendingUpdates(prev => {
      const newMap = new Map(prev);
      newMap.delete(optimisticId);
      return newMap;
    });
    
    setData(prev => prev.filter(item => item.id !== optimisticId));
  }, []);

  return {
    data,
    pendingUpdates,
    optimisticUpdate,
    retryPendingUpdate,
    cancelPendingUpdate,
  };
}
```

## Error Boundaries for API Errors

### API Error Boundary
```javascript
// components/ApiErrorBoundary.js
import React from 'react';

class ApiErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null, errorInfo: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    this.setState({
      error: error,
      errorInfo: errorInfo,
    });

    // Log error to monitoring service
    console.error('API Error Boundary caught an error:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="min-h-screen flex items-center justify-center bg-gray-50">
          <div className="max-w-md w-full bg-white shadow-lg rounded-lg p-6">
            <div className="flex items-center justify-center w-12 h-12 mx-auto bg-red-100 rounded-full">
              <svg className="w-6 h-6 text-red-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.964-.833-2.732 0L3.732 16.5c-.77.833.192 2.5 1.732 2.5z" />
              </svg>
            </div>
            <div className="mt-4 text-center">
              <h3 className="text-lg font-medium text-gray-900">
                Something went wrong
              </h3>
              <p className="mt-2 text-sm text-gray-500">
                We're sorry, but something unexpected happened. Please try again.
              </p>
              <div className="mt-4">
                <button
                  onClick={() => window.location.reload()}
                  className="inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-white bg-red-600 hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500"
                >
                  Reload Page
                </button>
              </div>
            </div>
          </div>
        </div>
      );
    }

    return this.props.children;
  }
}

export default ApiErrorBoundary;
```

## Complex State Synchronization

### Multi-Source State Management
```javascript
// hooks/useMultiSourceState.js
import { useState, useEffect, useCallback, useRef } from 'react';

export function useMultiSourceState(initialState = {}) {
  const [state, setState] = useState(initialState);
  const [isSyncing, setIsSyncing] = useState(false);
  const [lastSync, setLastSync] = useState(null);
  const syncTimeoutRef = useRef(null);

  // Debounced sync function
  const debouncedSync = useCallback((newState) => {
    if (syncTimeoutRef.current) {
      clearTimeout(syncTimeoutRef.current);
    }

    syncTimeoutRef.current = setTimeout(() => {
      setIsSyncing(true);
      // Simulate API sync
      setTimeout(() => {
        setLastSync(Date.now());
        setIsSyncing(false);
      }, 1000);
    }, 500);
  }, []);

  // Update state and trigger sync
  const updateState = useCallback((updates) => {
    setState(prev => ({ ...prev, ...updates }));
    debouncedSync(updates);
  }, [debouncedSync]);

  // Batch updates
  const batchUpdate = useCallback((updates) => {
    setState(prev => ({ ...prev, ...updates }));
    debouncedSync(updates);
  }, [debouncedSync]);

  // Reset state
  const resetState = useCallback(() => {
    setState(initialState);
    setLastSync(null);
    if (syncTimeoutRef.current) {
      clearTimeout(syncTimeoutRef.current);
    }
  }, [initialState]);

  // Force sync
  const forceSync = useCallback(() => {
    if (syncTimeoutRef.current) {
      clearTimeout(syncTimeoutRef.current);
    }
    setIsSyncing(true);
    setTimeout(() => {
      setLastSync(Date.now());
      setIsSyncing(false);
    }, 1000);
  }, []);

  // Cleanup on unmount
  useEffect(() => {
    return () => {
      if (syncTimeoutRef.current) {
        clearTimeout(syncTimeoutRef.current);
      }
    };
  }, []);

  return {
    state,
    updateState,
    batchUpdate,
    resetState,
    forceSync,
    isSyncing,
    lastSync,
  };
}
```

## Real-time Data Synchronization

### WebSocket Integration
```javascript
// hooks/useWebSocket.js
import { useState, useEffect, useCallback, useRef } from 'react';

export function useWebSocket(url, options = {}) {
  const [socket, setSocket] = useState(null);
  const [isConnected, setIsConnected] = useState(false);
  const [lastMessage, setLastMessage] = useState(null);
  const [error, setError] = useState(null);
  const reconnectTimeoutRef = useRef(null);
  const reconnectAttempts = useRef(0);
  const maxReconnectAttempts = options.maxReconnectAttempts || 5;

  const connect = useCallback(() => {
    try {
      const ws = new WebSocket(url);
      
      ws.onopen = () => {
        setIsConnected(true);
        setError(null);
        reconnectAttempts.current = 0;
        options.onOpen?.();
      };

      ws.onmessage = (event) => {
        const data = JSON.parse(event.data);
        setLastMessage(data);
        options.onMessage?.(data);
      };

      ws.onclose = (event) => {
        setIsConnected(false);
        setSocket(null);
        
        if (!event.wasClean && reconnectAttempts.current < maxReconnectAttempts) {
          reconnectAttempts.current++;
          const delay = Math.pow(2, reconnectAttempts.current) * 1000;
          
          reconnectTimeoutRef.current = setTimeout(() => {
            connect();
          }, delay);
        }
        
        options.onClose?.(event);
      };

      ws.onerror = (error) => {
        setError(error);
        options.onError?.(error);
      };

      setSocket(ws);
    } catch (error) {
      setError(error);
    }
  }, [url, options, maxReconnectAttempts]);

  const disconnect = useCallback(() => {
    if (reconnectTimeoutRef.current) {
      clearTimeout(reconnectTimeoutRef.current);
    }
    
    if (socket) {
      socket.close();
      setSocket(null);
    }
    
    setIsConnected(false);
  }, [socket]);

  const sendMessage = useCallback((message) => {
    if (socket && isConnected) {
      socket.send(JSON.stringify(message));
    }
  }, [socket, isConnected]);

  useEffect(() => {
    connect();
    
    return () => {
      disconnect();
    };
  }, [connect, disconnect]);

  return {
    socket,
    isConnected,
    lastMessage,
    error,
    sendMessage,
    connect,
    disconnect,
  };
}
```

## Exercise: Advanced State Management

Create a comprehensive state management system with:
- API context with caching
- Optimistic updates
- Error boundaries
- Multi-source state synchronization
- Real-time WebSocket integration
- Batch updates and debouncing

## Key Takeaways
- Implement advanced state management patterns
- Use context for API state management
- Implement optimistic updates for better UX
- Create error boundaries for API errors
- Handle complex state synchronization
- Use WebSockets for real-time updates
- Implement caching strategies
- Use debouncing for API calls
- Handle batch updates efficiently
- Manage pending updates
- Implement retry mechanisms
- Use proper error handling
- Optimize state updates
- Handle offline scenarios
- Implement proper cleanup
