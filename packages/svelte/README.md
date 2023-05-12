# @tryabby/svelte

## createAbby

### Parameters

The `createAbby` function takes an object as a parameter. The object can contain the following properties:

| Name               | Type            | Required | Description                                             | details               |
| ------------------ | --------------- | :------: | ------------------------------------------------------- | --------------------- |
| projectId          | `string`        |    ✅    | The ID of your project in Abby                          | -                     |
| apiUrl             | `string`        |          | The URL of the Abby API. Defaults to the hosted version | -                     |
| currentEnvironment | `string`        |    ✅    | The current environment of your application             | [link](/environments) |
| tests              | `object`        |          | An object containing your defined A/B Tests             | -                     |
| flags              | `Array<string>` |          | An array containing your defined Feature Flags          | -                     |
| settings           | `object`        |          | An object with additional settings for A/BBY            | -                     |

#### tests

The tests property is an object containing your defined A/B Tests. You probably want to use the Copy Button in your dashboard to copy the tests object.
They keys of the object represent the names of your predefined A/B tests. The values are objects containing the following properties:

| Name     | Type            | Required | Description                                             |
| -------- | --------------- | :------: | ------------------------------------------------------- |
| variants | `Array<string>` |    ✅    | An array of strings containing the variants of the test |

##### Example

```ts
const abby = createAbby({
  // ... your config
  tests: {
    "test-abtest": {
      variants: ["control", "variant-a", "variant-b"],
    },
  },
});
```

#### flags

The flags property is an array containing your defined Feature Flags. You probably want to use the Copy Button in your dashboard to copy the flags array.

##### Example

```ts
const abby = createAbby({
  // ... your config
  flags: ["test-flag"],
});
```

#### settings

The settings property is an object containing additional settings for A/BBY. The following properties are available:

- `flags.devDefault`: A boolean to set the default value of all feature flags in development mode. Defaults to `true`.

- `flags.default`: A boolean to set the default value of all feature flags in production mode. Defaults to `false`.

- `flags.devOverrides`: An object containing the values of feature flags in development mode. The keys of the object represent the names of the flags.
  The values are booleans.

### Return Values

#### useAbby

`useAbby` is a function providing acces to a store that is used to access the value of an A/B Test.
Recurring users will always get the same value for a test.
New users will get a random value for a test depending on the defined weights

##### Parameters

- `string`: The name of the test or flag, needs to be one of the defined tests.

##### Return Values

- `variant` : Store providing the variant of the test

- `onAct`: A function to call when the user performs an action associated with the test _Type: `function`_

#### useFeatureFlag

`useFeatureFlag` is a function providing acces to a store that is used to access the value of a Feature Flag.

##### Parameters

The name of the test or flag, needs to be one of the defined flags.

##### Return Value

A store providing the value of the flag _Type: `boolean`_

#### AbbyProvider

A svelte component to wrap your application. 

##### Props

- `children`: The children of the component
- `initialData (optional)`: The data (weights, tests, etc). If not provided, the data will be fetched on the client.

#### getFeatureFlagValue

`getFeatureFlagValue` is a function to access the value of a feature flag. This can be called in a non-svelte scope (Regular Typescript, Edge Functions and API Routes)

##### Parameters

The name of the test or flag, needs to be one of the defined flags.

#### getABTestValue

`getABTestValue` is a function to access the users variant of an A/B Test. This can be called in a non-svelte scope.
If the user is new, a random variant will be generated based on the weights, persisted in a cookie and returned.
Otherwise the variant will be read from the cookie and returned.

##### Parameters

The name of the test, needs to be one of the defined tests.

##### Return Values

The variant of the test.

#### \_\_abby\_\_

`__abby__` is the Abby instance. It can be used to access the data of the current user.
In most cases you will not need to use this.

#### withDevtools

`withDevtools` is a function providing a svelte component to enable the Devtools. The Devtools need to be within the 'AbbyProvider'.

##### Parameters

The Devtools component from `@tryabby/devtools`

##### Example

```jsx
import { AbbyDevTools } from "@tryabby/devtools";
export const AbbyDevtools = withDevtools(AbbyDevTools);
```

#### withAbby

A function that enables Server Side Rendering within Sveltekit
If you use this in your layout.server.ts, all occurances of `useFeatureFlag` will be SSR'd.

##### Example

```ts
// layout.server.ts
import { abby } from "$lib/abby";
 
export const load = abby.withAbby()
```

#### getABResetFunction

This is a function which returns a function that can be used to reset the stored variant for the current user.
This means the cookie will be deleted and the user will get a new variant on the next page load.

#### Parameters

The name of the test, needs to be one of the defined tests.

