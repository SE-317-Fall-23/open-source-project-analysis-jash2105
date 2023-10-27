# Assignment Submission


---

## Project Selected: TodoMVC

## I. Introduction
- The purpose of this section is to provide a detailed description of a sequence of test cases in the TodoMVC project. These test cases cover various aspects of the application, from adding todo items to trimming input.

## II. Test Case 1: Sequence Covering Todo Item Management
### A. Description
- This sequence of test cases is designed to verify or validate the process of managing todo items in the application. It includes individual test cases for adding items, clearing the text input field after an item is added, appending new items to the bottom of the list, and trimming text input.

### B. Gherkin Syntax (if applicable)
```gherkin
Scenario: Managing todo items
  Given I am on the TodoMVC homepage
  When I add a new todo item
  Then the todo item should be added to the list
  And the text input field should be cleared
  And new items should be appended to the bottom of the list
  And the text input should be trimmed
```

### C. Test Steps
1. Navigate to the TodoMVC homepage.
2. Add a new todo item.
3. Verify that the todo item is added to the list.
4. Verify that the text input field is cleared.
5. Verify that new items are appended to the bottom of the list.
6. Verify that the text input is trimmed.

### D. Code Segments Under Test
```javascript


    //tests
	test.it('should allow me to add todo items', function (done) {
		page.enterItem(TODO_ITEM_ONE);
		testOps.assertItems([TODO_ITEM_ONE]);
		page.enterItem(TODO_ITEM_TWO);
		testOps.assertItems([TODO_ITEM_ONE, TODO_ITEM_TWO])
			.then(function () { done(); });
	});

	test.it('should clear text input field when an item is added', function (done) {
		page.enterItem(TODO_ITEM_ONE);
		testOps.assertNewItemInputFieldText('')
			.then(function () { done(); });
	});

	test.it('should append new items to the bottom of the list', function (done) {
		createStandardItems();
		testOps.assertItemCount(3);
		testOps.assertItemText(0, TODO_ITEM_ONE);
		testOps.assertItemText(1, TODO_ITEM_TWO);
		testOps.assertItemText(2, TODO_ITEM_THREE)
			.then(function () { done(); });
	});

	test.it('should trim text input', function (done) {
		page.enterItem('   ' + TODO_ITEM_ONE + '  ');
		testOps.assertItemText(0, TODO_ITEM_ONE)
			.then(function () { done(); });
	});
	
	
	
	
//responsible logic
addTodo: function () {
    var value = this.newTodo && this.newTodo.trim();
    if (!value) {
        return;
    }
    this.todos.push({ id: Date.now(), title: value, completed: false });
    this.newTodo = '';
},

toggleItems(completed) {
    this.#elements.forEach((element) => {
        if (completed && element["item-completed"] === "false")
            element.toggleInput.click();
        else if (!completed && element["item-completed"] === "true")
            element.toggleInput.click();
    });
}

this.enterItem = function (itemText) {
		var self = this;
		var nItems;
		return self.getListItems()
			.then(function (items) {
				nItems = items.length;
			})
			.then(this.waitForNewItemInputField.bind(this))
			.then(function (newItemInput) {
				return newItemInput.sendKeys(itemText).then(function () { return newItemInput; });
			})
			.then(function (newItemInput) {
				return browser.wait(function () {
					// Hit Enter repeatedly until the text goes away
					return newItemInput.sendKeys(webdriver.Key.ENTER)
						.then(newItemInput.getAttribute.bind(newItemInput, 'value'))
						.then(function (newItemInputValue) {
							return newItemInputValue.length === 0;
						});
				}, DEFAULT_TIMEOUT);
			})
			.then(function () {
				return self.waitForElement(self.getLastListItemLabelCss(nItems));
			})
			.then(this.waitForTextContent.bind(this, itemText.trim(),
				'Expected new item label to read ' + itemText.trim()));
	};

    // Add a single todo item to the list by creating a view for it, and
    // appending its element to the `<ul>`.
    AppView.prototype.addOne = function (todo) {
        var view = new TodoView({ model: todo });
        this.$('.todo-list').append(view.render().el);
    };
    
    
    






```

### E. Initial State
- The initial state of the system is the TodoMVC homepage with no todo items in the list.

### F. Transition
- The actions that trigger a change in the system's state include adding a new todo item and verifying various aspects related to the todo item management.

### G. Expected State
- The expected outcome is that the todo item gets successfully added to the list, the text input field is cleared, new items are appended at the bottom, and the text input is trimmed.

---





---

## III. Test Case 2: Managing Item Completion State Including Bulk Operations
### A. Description
- This test case validates the application's behavior when items are marked as complete, unmarked as complete, and when the completion state for all items is cleared.

### B. Gherkin Syntax (if applicable)
```
Feature: Comprehensive management of item completion state
  Scenario: Mark, un-mark, and clear completion state of all items
    Given a list of todo items
    When I mark individual items as complete
    And I un-mark individual items
    And I clear the completion state of all items
    Then the final state of each item should match the expected state
```

### C. Test Steps
1. Initialize todo items by adding two new items.
2. Mark the first item as complete.
3. Validate that the first item is complete and the second is incomplete.
4. Mark the second item as complete.
5. Validate that both items are complete.
6. Un-mark the first item.
7. Validate that the first item is incomplete and the second is complete.
8. Click on "Mark All as Complete" checkbox twice to clear the completion state of all items.
9. Validate that all items are incomplete.

### D. Code Segments Under Test
Function Names:
- `test.it('should allow me to mark items as complete', function (done) {...})`
- `test.it('should allow me to un-mark items as complete', function (done) {...})`
- `test.it('should allow me to clear the completion state of all items', function (done) {...})`

```Javascript
// Marking and un-marking individual items
page.enterItem(TODO_ITEM_ONE);
page.enterItem(TODO_ITEM_TWO);
page.toggleItemAtIndex(0);
testOps.assertItemCompletedStates([true, false]);
page.toggleItemAtIndex(1);
testOps.assertItemCompletedStates([true, true]);
page.toggleItemAtIndex(0);
testOps.assertItemCompletedStates([false, true]);

// Clearing completion state of all items
page.clickMarkAllCompletedCheckBox();
page.clickMarkAllCompletedCheckBox();
testOps.assertItemCompletedStates([false, false, false]);


//responsible logic 
    
    toggleItem(event) {
    this.#data.forEach((entry) => {
        if (entry.id === event.detail.id)
            entry.completed = event.detail.completed;
    });

    this.update("toggle-item", event.detail.id);
}

this.toggleItemAtIndex = function (index) {
		return this.waitForToggleForItem(index).click();
	};
        
        
        
    this.enterItem = function (itemText) {
		var self = this;
		var nItems;
		return self.getListItems()
			.then(function (items) {
				nItems = items.length;
			})
			.then(this.waitForNewItemInputField.bind(this))
			.then(function (newItemInput) {
				return newItemInput.sendKeys(itemText).then(function () { return newItemInput; });
			})
			.then(function (newItemInput) {
				return browser.wait(function () {
					// Hit Enter repeatedly until the text goes away
					return newItemInput.sendKeys(webdriver.Key.ENTER)
						.then(newItemInput.getAttribute.bind(newItemInput, 'value'))
						.then(function (newItemInputValue) {
							return newItemInputValue.length === 0;
						});
				}, DEFAULT_TIMEOUT);
			})
			.then(function () {
				return self.waitForElement(self.getLastListItemLabelCss(nItems));
			})
			.then(this.waitForTextContent.bind(this, itemText.trim(),
				'Expected new item label to read ' + itemText.trim()));
	};

    // Add a single todo item to the list by creating a view for it, and
    // appending its element to the `<ul>`.
    AppView.prototype.addOne = function (todo) {
        var view = new TodoView({ model: todo });
        this.$('.todo-list').append(view.render().el);
    };
    
```

---

### E. Initial State
- The TodoMVC application is freshly loaded, and the browser's local storage is clear, ensuring no existing todo items are present. The interface displays no items, and the "Mark All as Complete" checkbox is neither visible nor active.

### F. Transition
1. **Adding New Items**: The first state transition occurs when new todo items are added to the list. This action makes the "Mark All as Complete" checkbox visible.

2. **Marking Items**: When an item is marked as complete, its visual style changes to indicate the completed status, and the "Clear Completed" button appears. This is the second state transition.

3. **Un-marking Items**: Un-marking a previously completed item reverts its visual style to the default, representing an incomplete status. If no items are left in the completed state, the "Clear Completed" button disappears. This is the third state transition.

4. **Bulk Operation**: Clicking the "Mark All as Complete" checkbox twice triggers the next transition. The first click marks all items as complete, and the second click reverses this action, marking all items as incomplete.

### G. Expected State
- After executing all test steps, the final expected state is that all todo items are present in the list but none are marked as complete. The "Clear Completed" button should not be visible, and the "Mark All as Complete" checkbox should be unchecked.


---
