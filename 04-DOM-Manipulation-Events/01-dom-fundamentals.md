# DOM Fundamentals and Manipulation

## Introduction
This lesson covers the fundamental concepts of the Document Object Model (DOM), including DOM structure, node types, traversal methods, and basic manipulation techniques.

## DOM Structure and Concepts

### Understanding the DOM Tree
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DOM Fundamentals</title>
</head>
<body>
    <div id="container" class="main-container">
        <header>
            <h1 id="main-title">Welcome to DOM Learning</h1>
            <nav>
                <ul class="nav-list">
                    <li><a href="#home" class="nav-link">Home</a></li>
                    <li><a href="#about" class="nav-link">About</a></li>
                    <li><a href="#contact" class="nav-link">Contact</a></li>
                </ul>
            </nav>
        </header>
        
        <main>
            <section id="content">
                <h2>Main Content</h2>
                <p class="description">This is a sample paragraph for DOM manipulation.</p>
                <div class="interactive-area">
                    <button id="add-btn" class="btn">Add Item</button>
                    <button id="remove-btn" class="btn">Remove Item</button>
                    <ul id="item-list" class="item-list">
                        <li class="list-item">Item 1</li>
                        <li class="list-item">Item 2</li>
                        <li class="list-item">Item 3</li>
                    </ul>
                </div>
            </section>
        </main>
        
        <footer>
            <p>&copy; 2024 DOM Learning</p>
        </footer>
    </div>
</body>
</html>
```

### DOM Node Types
```javascript
// Understanding DOM Node Types
function exploreNodeTypes() {
    const container = document.getElementById('container');
    
    // Node type constants
    console.log('Node Types:');
    console.log('ELEMENT_NODE:', Node.ELEMENT_NODE); // 1
    console.log('ATTRIBUTE_NODE:', Node.ATTRIBUTE_NODE); // 2
    console.log('TEXT_NODE:', Node.TEXT_NODE); // 3
    console.log('COMMENT_NODE:', Node.COMMENT_NODE); // 8
    console.log('DOCUMENT_NODE:', Node.DOCUMENT_NODE); // 9
    console.log('DOCUMENT_TYPE_NODE:', Node.DOCUMENT_TYPE_NODE); // 10
    
    // Check node types
    console.log('\nContainer node type:', container.nodeType);
    console.log('Container node name:', container.nodeName);
    console.log('Container node value:', container.nodeValue);
    
    // Check child nodes
    console.log('\nChild nodes:');
    container.childNodes.forEach((node, index) => {
        console.log(`Child ${index}:`, {
            type: node.nodeType,
            name: node.nodeName,
            value: node.nodeValue?.trim() || 'null'
        });
    });
}

// Run the exploration
exploreNodeTypes();
```

## DOM Selection Methods

### Basic Selection Methods
```javascript
// DOM Selection Methods
function demonstrateSelection() {
    // getElementById - Select by ID
    const container = document.getElementById('container');
    const mainTitle = document.getElementById('main-title');
    const addBtn = document.getElementById('add-btn');
    
    console.log('Selected by ID:', container, mainTitle, addBtn);
    
    // getElementsByClassName - Select by class name
    const navLinks = document.getElementsByClassName('nav-link');
    const listItems = document.getElementsByClassName('list-item');
    const buttons = document.getElementsByClassName('btn');
    
    console.log('Selected by class:', navLinks, listItems, buttons);
    
    // getElementsByTagName - Select by tag name
    const divs = document.getElementsByTagName('div');
    const paragraphs = document.getElementsByTagName('p');
    const links = document.getElementsByTagName('a');
    
    console.log('Selected by tag:', divs, paragraphs, links);
    
    // querySelector - Select first matching element
    const firstNavLink = document.querySelector('.nav-link');
    const firstListItem = document.querySelector('.list-item');
    const mainSection = document.querySelector('main section');
    
    console.log('Selected with querySelector:', firstNavLink, firstListItem, mainSection);
    
    // querySelectorAll - Select all matching elements
    const allNavLinks = document.querySelectorAll('.nav-link');
    const allListItems = document.querySelectorAll('.list-item');
    const allButtons = document.querySelectorAll('.btn');
    
    console.log('Selected with querySelectorAll:', allNavLinks, allListItems, allButtons);
    
    // Complex selectors
    const containerChildren = document.querySelectorAll('#container > *');
    const navLinksInList = document.querySelectorAll('nav ul li a');
    const buttonsInInteractiveArea = document.querySelectorAll('.interactive-area .btn');
    
    console.log('Complex selectors:', containerChildren, navLinksInList, buttonsInInteractiveArea);
}
```

### Advanced Selection Techniques
```javascript
// Advanced Selection Techniques
function advancedSelection() {
    // Select by attribute
    const elementsWithId = document.querySelectorAll('[id]');
    const elementsWithClass = document.querySelectorAll('[class]');
    const elementsWithHref = document.querySelectorAll('[href]');
    
    console.log('Elements with attributes:', elementsWithId, elementsWithClass, elementsWithHref);
    
    // Select by attribute value
    const elementsWithSpecificClass = document.querySelectorAll('[class*="nav"]');
    const elementsWithSpecificId = document.querySelectorAll('[id^="main"]');
    const elementsWithSpecificHref = document.querySelectorAll('[href$=".html"]');
    
    console.log('Elements with specific attributes:', elementsWithSpecificClass, elementsWithSpecificId, elementsWithSpecificHref);
    
    // Select by position
    const firstChild = document.querySelector('#container > :first-child');
    const lastChild = document.querySelector('#container > :last-child');
    const nthChild = document.querySelector('#container > :nth-child(2)');
    
    console.log('Position-based selection:', firstChild, lastChild, nthChild);
    
    // Select by state
    const visibleElements = document.querySelectorAll(':not([hidden])');
    const focusableElements = document.querySelectorAll('button, input, select, textarea, a[href]');
    
    console.log('State-based selection:', visibleElements, focusableElements);
}
```

## DOM Traversal Methods

### Node Traversal
```javascript
// DOM Traversal Methods
function demonstrateTraversal() {
    const container = document.getElementById('container');
    
    // Parent traversal
    console.log('Parent traversal:');
    console.log('Container parent:', container.parentNode);
    console.log('Container parent element:', container.parentElement);
    
    // Child traversal
    console.log('\nChild traversal:');
    console.log('Container children:', container.children);
    console.log('Container child nodes:', container.childNodes);
    console.log('First child:', container.firstChild);
    console.log('Last child:', container.lastChild);
    console.log('First element child:', container.firstElementChild);
    console.log('Last element child:', container.lastElementChild);
    
    // Sibling traversal
    console.log('\nSibling traversal:');
    const mainTitle = document.getElementById('main-title');
    console.log('Main title next sibling:', mainTitle.nextSibling);
    console.log('Main title previous sibling:', mainTitle.previousSibling);
    console.log('Main title next element sibling:', mainTitle.nextElementSibling);
    console.log('Main title previous element sibling:', mainTitle.previousElementSibling);
    
    // Traverse up the tree
    console.log('\nUpward traversal:');
    let current = mainTitle;
    let level = 0;
    while (current && current !== document.documentElement) {
        console.log(`Level ${level}:`, current.tagName, current.className);
        current = current.parentElement;
        level++;
    }
    
    // Traverse down the tree
    console.log('\nDownward traversal:');
    function traverseDown(element, level = 0) {
        console.log(`${'  '.repeat(level)}${element.tagName}${element.className ? '.' + element.className : ''}`);
        Array.from(element.children).forEach(child => {
            traverseDown(child, level + 1);
        });
    }
    
    traverseDown(container);
}
```

## Basic DOM Manipulation

### Creating and Adding Elements
```javascript
// Creating and Adding Elements
function createAndAddElements() {
    const container = document.getElementById('container');
    
    // Create new elements
    const newDiv = document.createElement('div');
    const newParagraph = document.createElement('p');
    const newButton = document.createElement('button');
    
    // Set attributes
    newDiv.className = 'new-section';
    newDiv.id = 'dynamic-section';
    newParagraph.textContent = 'This is a dynamically created paragraph.';
    newButton.textContent = 'Dynamic Button';
    newButton.className = 'btn dynamic-btn';
    
    // Add event listener
    newButton.addEventListener('click', function() {
        alert('Dynamic button clicked!');
    });
    
    // Append elements
    newDiv.appendChild(newParagraph);
    newDiv.appendChild(newButton);
    container.appendChild(newDiv);
    
    // Insert before existing element
    const existingSection = document.getElementById('content');
    const newSection = document.createElement('section');
    newSection.className = 'inserted-section';
    newSection.innerHTML = '<h3>Inserted Section</h3><p>This section was inserted before the content.</p>';
    
    container.insertBefore(newSection, existingSection);
    
    // Insert after existing element
    const newSectionAfter = document.createElement('section');
    newSectionAfter.className = 'inserted-after-section';
    newSectionAfter.innerHTML = '<h3>Inserted After Section</h3><p>This section was inserted after the content.</p>';
    
    existingSection.parentNode.insertBefore(newSectionAfter, existingSection.nextSibling);
}
```

### Modifying Element Content
```javascript
// Modifying Element Content
function modifyElementContent() {
    const mainTitle = document.getElementById('main-title');
    const description = document.querySelector('.description');
    
    // Modify text content
    console.log('Original title:', mainTitle.textContent);
    mainTitle.textContent = 'Updated DOM Learning Title';
    console.log('Updated title:', mainTitle.textContent);
    
    // Modify inner HTML
    console.log('Original description:', description.innerHTML);
    description.innerHTML = 'This is an <strong>updated</strong> paragraph with <em>HTML</em> content.';
    console.log('Updated description:', description.innerHTML);
    
    // Modify outer HTML (replace entire element)
    const oldButton = document.getElementById('add-btn');
    const newButton = document.createElement('button');
    newButton.id = 'add-btn';
    newButton.className = 'btn updated-btn';
    newButton.textContent = 'Updated Add Button';
    
    oldButton.parentNode.replaceChild(newButton, oldButton);
    
    // Modify attributes
    const container = document.getElementById('container');
    container.setAttribute('data-modified', 'true');
    container.setAttribute('data-timestamp', Date.now());
    
    console.log('Container attributes:', {
        id: container.getAttribute('id'),
        class: container.getAttribute('class'),
        'data-modified': container.getAttribute('data-modified'),
        'data-timestamp': container.getAttribute('data-timestamp')
    });
}
```

### Removing Elements
```javascript
// Removing Elements
function removeElements() {
    // Remove by element reference
    const lastListItem = document.querySelector('.list-item:last-child');
    if (lastListItem) {
        lastListItem.remove();
        console.log('Removed last list item');
    }
    
    // Remove by parent
    const firstListItem = document.querySelector('.list-item:first-child');
    if (firstListItem) {
        firstListItem.parentNode.removeChild(firstListItem);
        console.log('Removed first list item using parent');
    }
    
    // Remove all elements with specific class
    const dynamicElements = document.querySelectorAll('.dynamic-btn, .inserted-section, .inserted-after-section');
    dynamicElements.forEach(element => {
        element.remove();
        console.log('Removed dynamic element:', element.className);
    });
    
    // Clear content
    const itemList = document.getElementById('item-list');
    itemList.innerHTML = '';
    console.log('Cleared item list content');
    
    // Remove attributes
    const container = document.getElementById('container');
    container.removeAttribute('data-modified');
    container.removeAttribute('data-timestamp');
    console.log('Removed data attributes from container');
}
```

## Exercise: DOM Fundamentals

Practice DOM manipulation with:
- Element selection and traversal
- Creating and adding elements
- Modifying element content
- Removing elements
- Working with attributes
- Understanding node types

## Key Takeaways
- Understand DOM structure and node types
- Master element selection methods
- Use DOM traversal techniques
- Create and manipulate elements
- Modify element content and attributes
- Remove elements safely
- Understand parent-child relationships
- Use proper DOM methods
- Handle text nodes vs element nodes
- Work with element collections
- Use modern DOM methods
- Understand DOM tree structure
- Practice element manipulation
- Use proper event handling
- Understand DOM performance
