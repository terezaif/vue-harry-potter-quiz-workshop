#Step2: Create the first own Vue component "Quiz.vue"

Now we are going to write our first own component in Vue. All components are also Vue instances.

Our Vue projects actually provides a folder structure which is almost valid for many js frameworks. In the components folder all vue components are stored. Here you can also find the HelloWorld.vue component which contains all the helping text.

Create a quiz component Quiz.vue inside the components folder.

![components folder](img/components.png)

The file will be empty so far.

Normally Vue components contain a template, a script and a style section.
The smallest Vue component looks like that:

```html
<template>
  <div></div>
</template>

<script>export default {};</script>

<style></style>
```

It doesn’t provide any meaningful output. Let us put some text within the <div> containers, to see when we are using it, it is used.

```html
<template>
  <div>Quiz</div>
</template>
```

Now, we are including out brand new component in the App.vue. Just remove the `<HelloWorld />` component and use the Quiz component instead.

To use our Quiz component some steps are needed. First we have to import the component within the script section.

```javascript
import Quiz from "./components/Quiz.vue";
```

After that, the Quiz component has to be registered within the App.vue, which is done by adding the imported Quiz  to the App components options.

```javascript
components: {
    Quiz
}
```

This above is the easiest way to do that. It will map your component Quiz to the component element `<Quiz />`. But is also possible to register the component with more settings instead. e.g. with e given name for the component name:

```javascript
components: {
    'quiz': Quiz
}
```

The last thing to do is to use the component element `<quiz />` in the template.

```html
<template>
  <div id="app">
    <quiz/>
  </div>
</template>
```

Before the Quiz starts, we need a welcome view showed to the user.
In the Quiz.vue template section we place a picture, a title and a link, which finally starts the quiz.

```html
<div>
    <img
      src="https://media0.giphy.com/media/Bh3YfliwBZNwk/giphy.gif?cid=3640f6095c852266776c6f746fb2fc67"
      alt
    >
    <h1 class="quiz-heading">How Well Do You Know the Harry Potter Movies?</h1>
    <button class="quiz-button" @click="initQuizStage">Start Quiz</button>
</div>
```

For more styling you can use the style section and write your css.

Within the App.vue we set some general settings, like having a dark background and defining the main text color:

```css
/* App.vue */
<style>
* {
  box-sizing: border-box;
}
html {
  height: 100%;
}
body {
  height: 100%;
  background: #020815;
  color: #eee;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}
</style>
```

For the Quiz.vue we can make some simple stylings like the following.

```css
/* Quiz.vue */
<style>
.quiz-heading {
  margin: -40px 0 30px;
  font-size: 30px;
  text-shadow: 1px 1px 2px #020815;
  line-height: 1.2;
}

.quiz-button {
  color: #eee;
  text-decoration: none;
  cursor: pointer;
  display: inline-block;
  padding: 10px 30px;
  border: 1px solid rgba(238, 238, 238, 0.3);
  background: none;
  transition: border-color 0.5s;
}
.quiz-button:hover {
  border-color: #eee;
}
</style>
```

When you click on the link to start the quiz, nothings happens, only the link in the navigation bar changes. What we need let something happen when the link is clicked, we need to bin an action t o the link itself. This we could achieved with adding an event modifier `@click` as attribute of the link.

```javascript
@click="initQuizStage"
```

As value it contains initQuizStage where Vue expects a method, which is called when user clicks on the link. Wherefore we are going to define a method in the script section of our component.

```javascript
// Quiz.js
<script>
export default {
  methods: {
    initQuizStage() {
      console.log("Start the quiz....");
    }
  }
};
</script>
```

It only prints “Start the quiz…” to the console. Before ongoing and implementing the `initQuizStage` we need more data. We need the movie titles of all Harry Potter movies. But also the data for quiz questions.

Here we are going to provide all movie titles to our quiz component. Providing data to child components can be done with props. Props are custom attributes you can register on a component. A value can be passed to a prop attribute, which becomes a property on that component instance.

In the App.vue extend the quiz element with an props attribute called “movies” and provide it with movies data, which you get from the data() method. With the colon in front of the prop name, you are telling Vue that this value inside the brackets is not just a string but a variable.

```html
<!-- App.vue -->
<template>
  <div id="app">
    <quiz :movies="movies"/>
  </div>
</template>

<script>
export default {
 // ...
data() {
    return {
      movies: [
        "Harry Potter and the Philosopher's Stone",
        "Harry Potter and the Chamber of Secrets",
        "Harry Potter and the Prisoner of Azkaban",
        "Harry Potter and the Goblet of Fire",
        "Harry Potter and the Order of the Phoenix",
        "Harry Potter and the Half-Blood Prince",
        "Harry Potter and the Deathly Hallows - Part 1",
        "Harry Potter and the Deathly Hallows - Part 2"
      ]
    };
  }
}
```

If you provide a prop to the component, the component has to define that prop on the other side. This is done with introducing the prop in the `props: {}` section.

```html
<!-- Quiz.vue -->
<script>
export default {
  props: {
    movies: {
      type: Array,
      required: true
    }
  },
// ...
</script>
```

To see if it works, we can use a simple list and iterate over the entries of movies.

```html
<!-- Quiz.vue -->
<ul>
    <li v-for="movie in movies" :key="movie">{{ movie }}</li>
</ul>
```

With the `v-for` directive we can tell vue to iterate over the values in movie and to repeat this element with each value provided during each iteration.
The `{{ movie }}` is the mustache syntax. The mustache tag will be replaced with the value of the movie property on the corresponding data object. It will also be updated whenever the data object’s movie property changes.

Let’s ensure that the part with the printed movies list is only shown when `initQuizStage` is clicked. This can be achieved by using the `stage` computed property and with the `v-if` directive in the template. The `v-if` directive validates the expression of its content. When it is true, the component is rendered and shown, if not, it is not rendered.

```html
<!-- Quiz.vue -->
<template>
  <ul class="quiz-choices" v-if="stage===’quiz’">
<template>

<script>
export default {
  props: {
    movies: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      currentQuestionNo: 0
    };
  },
  computed: {
    // ....
    stage() {
        !store.currentQuestionNo ? “welcome” : “quiz”;
    }
  },
  methods: {
    initQuizStage() {
      this.currentQuestionNo = 1;
  }
};
</script>
```

Also let ensure that the “Start Quiz” button disappears when the Quiz is started. We can again use the `stage` property for it.

```html
<button class="quiz-button" v-if="stage===’welcome’" @click="initQuizStage">Start Quiz</button>
```