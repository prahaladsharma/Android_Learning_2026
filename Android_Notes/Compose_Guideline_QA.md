#  📱 Jetpack Compose API Design – Interview Q&A (Practical)

#### 1️⃣ Why is API design important in Jetpack Compose?

✅ Answer

In Jetpack Compose, every composable is an API. Poor API design leads to unreadable call sites, hard-to-maintain UI, and breaking changes. Good API design improves reusability, testability, and developer experience.


```kotlin
// ❌ Bad Example
MyButton("Login", true, false) { }
```

```kotlin
✅ Good Example
MyButton(
    text = "Login",
    enabled = true,
    isLoading = false,
    onClick = { }
)
```


### 👉 Interview takeaway: Compose APIs must be optimized for call-site readability.

### 2️⃣ What does “call-site readability” mean in Compose?

✅ Answer

Call-site readability means that Composable usage should read like a sentence, even without checking implementation.

```kotlin
❌ Bad API
UserCard(user, true, false)
```
```kotlin
✅ Good API
UserCard(
    user = user,
    showAvatar = true,
    isVerified = false
)
```

### 👉 Why it matters: Most Compose code is read, not written.

### 3️⃣ Why should composables be stateless?

### ✅ Answer

Stateless composables:

*   Are easier to test.

*   Are easier to reuse.

*   Avoid lifecycle issues.

*   Work better with ViewModel and MVI.

```kotlin
❌ Stateful Composable
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }
    Button(onClick = { count++ }) {
        Text("$count")
    }
}
```

```kotlin
✅ Stateless Composable
@Composable
fun Counter(
    count: Int,
    onIncrement: () -> Unit
) {
    Button(onClick = onIncrement) {
        Text("$count")
    }
}
```


### 👉 Interview insight: State belongs to ViewModel, not UI.

### 4️⃣ What is state hoisting? Explain with an example.

### ✅ Answer

State hoisting means moving state up to the caller and exposing changes via callbacks.

```kotlin
❌ Without State Hoisting
@Composable
fun NameInput() {
    var name by remember { mutableStateOf("") }
    TextField(name, onValueChange = { name = it })
}
```

```kotlin
✅ With State Hoisting
@Composable
fun NameInput(
    value: String,
    onValueChange: (String) -> Unit
) {
    TextField(value, onValueChange)
}
```

👉 Interview tip: Hoisted state = reusable + testable UI.

### 5️⃣ Why should Modifier be the first optional parameter?

### ✅ Answer

This follows Compose conventions and allows easy customization without modifying APIs.

```kotlin ❌ Wrong Order
@Composable
fun ProfileCard(name: String, modifier: Modifier)
```

```kotlin ✅ Correct Order
@Composable
fun ProfileCard(
    modifier: Modifier = Modifier,
    name: String
)
```

###👉 Interview expectation: You must know this rule.

### 6️⃣ What is “Boolean Explosion” and how do you avoid it?

### ✅ Answer

Boolean explosion happens when too many Boolean parameters make APIs confusing.

```kotlin ❌ Bad API
MyButton(
    enabled = true,
    isLoading = false,
    isOutlined = true,
    hasError = false
)
```

```kotlin ✅ Better Solution (State Object)
sealed class ButtonState {
    object Normal : ButtonState()
    object Loading : ButtonState()
    object Disabled : ButtonState()
}

MyButton(state = ButtonState.Loading)
```

### 👉 Interview insight: Prefer types over flags.

### 7️⃣ How do you expose customization without leaking implementation?

### ✅ Answer

Expose intent, not internal UI details.

```kotlin ❌ Overexposed API
fun AppButton(
    textColor: Color,
    bgColor: Color,
    cornerRadius: Dp
)
```

```kotlin ✅ Clean API
fun AppButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier
)
```

###👉 Customization should come via:

*   Modifier

*   slots

*   themes

### 8️⃣ What are slot APIs in Compose?
### ✅ Answer

Slot APIs allow callers to inject UI instead of hardcoding it.

```kotlin
Example
@Composable
fun AppScaffold(
    topBar: @Composable () -> Unit,
    content: @Composable () -> Unit
) {
    Column {
        topBar()
        content()
    }
}
```

### 👉 Interview angle: Slot APIs increase flexibility and composability.

### 9️⃣ Why should defaults be easy in Compose APIs?

### ✅ Answer

Most users want simple usage. Advanced customization should be optional.

```kotlin ✅ Ideal API
AppButton(
    text = "Submit",
    onClick = { }
)
```
No unnecessary parameters.

### 👉 Rule: Simple use case should require minimum arguments.

### 🔟 How do you design a reusable Compose Button?

```kotlin ✅ Answer with Code
@Composable
fun AppButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    isLoading: Boolean = false
) {
    Button(
        onClick = onClick,
        enabled = enabled && !isLoading,
        modifier = modifier
    ) {
        if (isLoading) {
            CircularProgressIndicator(
                modifier = Modifier.size(16.dp)
            )
        } else {
            Text(text)
        }
    }
}
```

### Why this is good:

✔ Readable.

✔ Stateless.

✔ Flexible.

✔ Follows Compose conventions.

### 1️⃣1️⃣ How does MVI benefit Compose API design?

### ✅ Answer

### MVI enforces:

*   Single immutable UI state.

*   Predictable rendering.

*   Event-driven updates.

```kotlin Example
data class UiState(
    val loading: Boolean = false,
    val data: List<Item> = emptyList(),
    val error: String? = null
)
```

### Compose becomes:

```kotlin
@Composable
fun Screen(state: UiState) {
    when {
        state.loading -> Loading()
        state.error != null -> Error(state.error)
        else -> Content(state.data)
    }
}
```

### 👉 Interview insight: Compose + MVI = perfect match.

### 1️⃣2️⃣ How do you test Compose APIs easily?

### ✅ Answer

Stateless composables + reducers = easy tests.

```kotlin Reducer Example
fun reduce(old: UiState, event: UiEvent): UiState {
    return when (event) {
        UiEvent.Load -> old.copy(loading = true)
    }
}
```

✔ No Android dependency

✔ Pure function

✔ Easy unit tests

### ⭐ Final Interview Takeaway

Great Jetpack Compose developers don’t just write UI — they design APIs.

