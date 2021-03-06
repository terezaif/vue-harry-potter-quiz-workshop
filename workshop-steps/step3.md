# Step 3: Using the Quiz data

Besides the movie data, we also need the quiz data with the movie clip and the choices of which movie it is.  
We'll get this data from a JSON (Javascript Object Notation) file.  
JSON is the description of an object in a more human readable way. It is mainly used to transfer information between systems.

You can read more about JSON here:  
- https://en.wikipedia.org/wiki/JSON
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON

The JSON contains a list (an array) of questions. Each question contains 4 different numbers, which matches to the movies we are going to provide as labels to the buttons of answer choices. It also contains the correct answer and furthermore the movie scene as link to the giphy image, which has to be guessed.


```json
{
  "questions": [
    {
      "correct": 3,
      "answers": [2, 3, 4, 5],
      "img": "https://media1.giphy.com/media/26BRzozg4TCBXv6QU/giphy.gif"
    },
    {
      "correct": 7,
      "answers": [6, 5, 7, 8],
      "img": "https://media2.giphy.com/media/Th8FaGYsmPMwU/giphy.gif"
    },
    {...}
  ]
}
```

To learn more about data primitives, like string, numbers and arrays, which are useful to understand the JSON structure better, we warmly recommend Lydia Hallie. She has written a very good explanation about it:
- https://www.theavocoder.com/complete-javascript/2018/12/18/primitive-data-types

To load the questions we're going to provide the Quiz component with the `questions-url` props from where the questions can be loaded. We'll use this to fetch the data from there.  
The fetching magic will happen with the `mounted()` method of our Quiz component. The `mounted()` method is a lifecycle hook, which is called after the instance has been mounted.

You can read more about Vue Mounted here:  
- https://vuejs.org/v2/api/#mounted

```html
<!-- App.vue -->
<quiz :movies="movies" questions-url="https://api.jsonbin.io/b/5e3f0514f47af813bad11ac5"/>
```

```javascript
// Quiz.vue
props: {
    movies: {
      type: Array,
      required: true
    },
    questionsUrl: {
      type: String,
      required: true,
    }
},
data() {
    return {
      questions: [],
      currentQuestionNumber: 0
    };
  },

// ...
async mounted() {
    const res = await fetch(this.questionsUrl);
    this.questions = (await res.json()).questions;
    console.log(this.questions);
},
```

You can read more about Vue Async Components here:  
- https://vuejs.org/v2/guide/components-dynamic-async.html#Async-Components

Now we're able to use these loaded questions to enhance the `initQuizStage` and use this data to provide the first question to the user.

We'll start replacing the image we used so far in the Quiz component to be changed dynamically in terms of the question we're showing. To do so some steps are needed.  

First, we need to know which question is in which order. We'll store the information within the `currentQuestionNumber` instance property. Then, we have to provide the according image, which we can do by using a computed property.

A computed property in Vue is an instance property as well, but the main advantage it's that it can be built by different properties together. Vue will watch for changes inside dependent properties, and if they change, the computed property will be evaluated again. On the other hand, it'll be kept cached and only the cached value is provided.

You can read more about Vue Computed Caching here:  
- https://vuejs.org/v2/guide/computed.html#Computed-Caching-vs-Methods

```html
<!-- Quiz.vue -->
<template>
    <div>
    <img :src="image" alt>
    ...
```

```javascript
/* Quiz.vue */
<script>
  export default {
    // ...
    computed: {
      image() {
        return this.currentQuestionNumber
          ? this.questions[this.currentQuestionNumber].img
          : "https://media0.giphy.com/media/Bh3YfliwBZNwk/giphy.gif?cid=3640f6095c852266776c6f746fb2fc67";
      }
    },
  // ...
```

We're going to do something similar for the title, because with the start of the script we want to change the title from “How Well Do You Know the Harry Potter Movies?” to “Which movie is this?”.

```javascript
// Quiz.vue
computed: {
    title() {
      return this.currentQuestionNumber
        ? "Which movie is this?"
        : "How Well Do You Know the Harry Potter Movies?";

      /*
        FYI: We're using a ternary operator in JS.
        The above code is equal to this:
        if (this.currentQuestionNumber) { 
          "Which movie is this?"
        } else {
          "How Well Do You Know the Harry Potter Movies?"
        }
        
        Learn about ternary operators here:
        - https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator
      */
    }
},
```

At last, let's add the title via text interpolation (Mustache syntax) into our template inside **Quiz.vue**.

You can read more about Vue Text Interpolation here:
- https://vuejs.org/v2/guide/syntax.html#Interpolations

```html
  <h1 class="quiz-heading">{{ title }}</h1>
```

---

[Prev: Step #2 - Create the first own Vue component "Quiz.vue"](step2.md)

[Next: Step #4 - Displaying possible movie options to user](step4.md)
