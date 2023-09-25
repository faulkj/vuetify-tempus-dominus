# VuetifyTempusDominus

## Introduction

`VuetifyTempusDominus` is a Vue 3 component that integrates Tempus Dominus DateTime Picker with Vuetify 3. Benefit from the rich set of features for time and date selection, all conforming to Vuetify's style guidelines.

## Installation

```bash
npm install vuetify-tempus-dominus
```

## Usage Guide

### Importing the Component

```ts
import VuetifyTempusDominus from 'vuetify-tempus-dominus'
```

### Basic Usage

```html
<template>
  <VuetifyTempusDominus :options="yourOptions"></VuetifyTempusDominus>
</template>
```

### Props

- options (Object): Tempus Dominus options. Refer to the [Tempus Dominus](https://tempusdominus.github.io/bootstrap-4/Options/) documentation.

### Vuetify Attributes and Slots
The component supports all Vuetify [v-text-field](https://vuetifyjs.com/en/components/text-fields/) attributes and slots.

### Events

- picker:init: Emitted when the picker is initialized. The Tempus Dominus object is passed back to the parent.
- picker:change: Emitted when the date or time changes.
- The component captures all Tempus Dominus events and emits them for further handling. You can listen to these events in the parent component and they'll be prefixed with `picker:`. For example, Tempus Dominus's `hide` event can be listened to as `picker:hide` in the parent component

## Examples

### Basic Example

```html
<template>
  <VuetifyTempusDominus :options="{ useCurrent: false }"></VuetifyTempusDominus>
</template>
```

### With Vuetify attributes

```html
<template>
  <VuetifyTempusDominus
    :options="{ debug: true, useCurrent: false }"
    label="Select Date"
    density="compact"
    outlined
  ></VuetifyTempusDominus>
</template>
```

### Using Slots

```html
<template>
  <VuetifyTempusDominus>
    <template v-slot:prepend>
      <v-icon>mdi-calendar</v-icon>
    </template>
    <template v-slot:append-inner></template>
  </VuetifyTempusDominus>
</template>
```

### Accessing Tempus Dominus Object

Here's how you can access the Tempus Dominus object for further manipulation:

```html
<template>
  <VuetifyTempusDominus
    @picker:init="handlePickerInit"
  ></VuetifyTempusDominus>
</template>

<script>
export default {
  methods: {
    handlePickerInit(picker) {
      // Now you can use 'picker' to manipulate the Tempus Dominus instance
      picker.show()
    }
  }
}
</script>
```

#### Example of Capturing Events

Here's how you can capture and handle the Tempus Dominus `hide` event:

```html
<template>
  <VuetifyTempusDominus
    @picker:hide="handlePickerHide"
  ></VuetifyTempusDominus>
</template>

<script>
export default {
  methods: {
    handlePickerHide(date) {
      console.log('Picker hidden, selected date is:', date);
    }
  }
}
</script>
