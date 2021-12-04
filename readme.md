# tabbed-box
This is an example of a basic tabbed box which allows the user to select a tab and view the contents of that tab. Feel free to also take a look at:
* Our [YouTube Video](https://www.youtube.com/watch?v=vxY4VLvl38o&t=312s), where we code this in real time with some music.
* Our [Dev Page](https://bytethisstore.com/articles/pg/js-tabbed-box), where we cover this topic in more detail.

## Demo the Live Example
The live example can be viewed in action on [this page](https://htmlpreview.github.io/?https://github.com/ByteThisStore/tabbed-box/blob/main/index.html).

This example covers:
1. Defining the tab titles and contents within the HTML markup.
1. Using JavaScript to manage the creation of the tabbed box container and remove some redundant markup.
1. Using CSS to style the box contents.

## Tabbed Box Requirements
In this example, we will create a simple, but fully functional tabbed box element. We want this tabbed box to behave according to the rules below:
* The tabbed box should allow for a variable number of tabs; the count should not be hard-coded.
* The element should only display contents of the currently selected tab.
* The user should be able to switch between selected tabs by clicking on tabs.
* We must always have one and only one tab selected at any given time.
* Selecting a tab which has already been selected should do nothing.
* We should be able to have more than one tabbed box instance if we want to.
We'll outline how to create the tabbed box element and satisfy these requirements in the sections below.

## Create the Tabbed Box Elements
There are a few different types of controls / elements we need to render in order to bring everything together:
* The container for the tabbed box.
* The individual tabs themselves.
* The contents corresponding to each tab.
There are many ways we can create these elements. For this example, we'll use one way that will use JavaScript processing to allow us to write less HTML to specify the tabs and contents. Instead of creating individual tab elements, then indivial contents elements, we'll combine the two by creating elements with class **tabbed-box** to represent tabs with their contents. We'll use **data-title** as a custom attribute to specify the tab name to use for those contents:
```html
<div id="example-box" class="tabbed-box-container">
    <!-- We'll define each tab within a class "tabbed-box" -->
    <!-- The title will be stored in "data-title" -->
    <div class="tabbed-box" data-title="Left">
        <p>This is the leftmost box.</p>
    </div>
    <div class="tabbed-box" data-title="Middle">
        <p>This is the middle box.</p>
        <p id="like-p">
            Don't forget to follow us on social media!
        </p>
    </div>
    <div class="tabbed-box" data-title="Right">
        <p>This is the rightmost box.</p>
    </div>
</div>
```
Note that at this point, we do not have any actual tab elements. In the section below, we will use JavaScript to create those for us.

## Creating and Processing the Tabbed Box
Now that we have our HTML together, we can use JavaScript to process the tabbed box, add tabs, and select a default tab to display. To accomplish this, we'll use a class. When we create a new instance, we'll pass in the id of the element which will be a tabbed box into it's constructor, and the class will process from there. In the code below, we will:
1. Select the tabbed box container which has the div id we've passed in and store it in a JavaScript variable.
1. Select all tabbed box elements within that container and store them in an array.
1. Create an element which will contain the new tab elements we are about to create.
1. For each tab box selected in the steps above, create a tab element and add it to our tab row.
1. Add the tab row itself to the container.
1. Mark that the container has been processed by changing it's class name to **tabbed-box-container-processed**.
```javascript
class TabbedBox {
    constructor(divId) {
        console.log(`Initializing tabbed box for ${divId}`);
        this.init(divId);
    }

    init(divId) {
        //get element refs
        this.containerElement = document.getElementById(divId);
        this.tabbedBoxElements = Array.from(
            this.containerElement.querySelectorAll(".tabbed-box")
        );

        //create tab objects
        const tabRow = document.createElement('div');
        tabRow.classList.add("tabbed-container-tab-row");

        this.tabElements = [];

        //for each tabbed box, create the tab element and add to the tabs row
        this.tabbedBoxElements.forEach((tab, index) => {
            const tabDiv = document.createElement("div");
            tabDiv.classList.add("tabbed-box-tab");
            tabDiv.innerText = tab.dataset.title;
            tabRow.appendChild(tabDiv);

            this.tabElements.push(tabDiv);
        });

        //add row and process container
        this.containerElement.insertBefore(
            tabRow,
            this.containerElement.firstElementChild
        );
        //mark that we've processed this box and let css rules take over
        this.containerElement.classList.remove("tabbed-box-container");
        this.containerElement.classList.add("tabbed-box-container-processed");
    }
}
```
In terms of JavaScript, we're almost done. We need to add one more piece of functionality: the ability to select different tabs. We'll have the tabbed box select the first tab by default upon its creation, then add a click listener to change the selected tab when the user clicks on a tab element. We'll use CSS class names to mark which tab is selected and which ones are not.
```javascript
class TabbedBox {
    init(divId) {
        /* ... previous code ... */

        //add click listeners to each tab item in this loop
        this.tabbedBoxElements.forEach((tab, index) => {
            /* ... previous code ... */

            //listen to click to select tab
            tabDiv.addEventListener("click", () => {
                this.selectBox(index);
            });
        });

        //set default to first box
        this.selectBox(0);
    }

    selectBox(index) {
        //for each tab element, use classes to mark it as not selected, except for the newly selected one
        this.tabbedBoxElements.forEach((el, forIndex) => {
            if (forIndex === index) {
                el.classList.add("tabbed-box-selected");
                el.classList.remove("tabbed-box");
            } else {
                el.classList.remove("tabbed-box-selected");
                el.classList.add("tabbed-box");
            }
        });

        this.tabElements.forEach((el, forIndex) => {
            if (forIndex === index) {
                el.classList.add("tabbed-box-tab-selected");
            } else {
                el.classList.remove("tabbed-box-tab-selected");
            }
        });
    }
}
```

## Styling the Elements for Proper Behavior
With the HTML and JavaScript in place, the behavior will "technically" work, but without the CSS, the tabbed box will appear broken. There are a few things we need to do with the CSS to fix this:
1. Make sure tabbed boxes are not shown until they are processed by JavaScript
1. Give the entire element a distinct border to seperate it from other elements on the page.
1. Make non-selected tabbed boxes hidden.
1. Make tabs themselves appear to be clickable (cursor) and have a hover effect.
The minimal css below will allow us to satisfy these requirements:
```css
/* Invisible until JavaScript processes it */
.tabbed-box-container {
    display: none;
}

.tabbed-box-container-processed {
    display: block;
    border: 2px solid black;
}

/* Unless selected, tabbed box contents are hidden */
.tabbed-box {
    display: none;
}

.tabbed-box-selected {
    display: block;
}

/* Flex row so tabs appear in a horizontal row */
.tabbed-container-tab-row {
    display: flex;
    flex-direction: row;
}

.tabbed-box-tab {
    border: 2px solid black;
    cursor: pointer;
    flex-grow: 1;
}

.tabbed-box-tab:hover {
    background-color: #ffc8ff;
}

.tabbed-box-tab-selected {
    background-color: #d87df7;
}
```

## Putting it All Together
When we combine the HTML, JavaScript, and CSS above, the following sequence of events will take place:
1. The tabbed box will be hidden when the document loads.
1. Some JavaScript will create a new tabbed box instance and provide the element id of the tabbed box.
1. The new instance will:
    1. Query each tabbed box within the container.
    1. Create a tab element for each.
    1. Add a click listener to select the corresponding tab contents when the tab is clicked.
    1. Use CSS classes to mark the currently selected box + that the processing has completed.
1. The tabbed box will now be visible and styled appropriately. The first tab will be selected by default.
1. When the user clicks on a tab, the selected tab will change via JavaScript CSS class name manipulation.