# To-Do List App

A simple and clean Flutter application for managing daily tasks. This app allows users to add, complete, and delete tasks, with all data stored locally on the device.

## Features

- 📝 Add new tasks
- ✅ Mark tasks as completed
- 🗑️ Delete tasks
- 💾 Local storage persistence
- 📱 Clean and intuitive UI
- 🌐 Works offline

## Project Structure

```
lib/
├── main.dart              # Main application file
```

## Dependencies

- `flutter`: Core Flutter framework
- `shared_preferences: ^2.2.2`: For local storage
- `cupertino_icons: ^1.0.2`: For iOS-style icons

## Implementation Details

### Data Model
The app uses a simple `Todo` class to represent tasks:
```dart
class Todo {
  final String id;
  final String title;
  bool isCompleted;
}
```

### Storage
- Uses `SharedPreferences` for local storage
- Tasks are stored as JSON strings
- Data persists between app sessions

### UI Components
- Material Design components
- Custom color scheme with blue as primary color
- Responsive layout
- ListView for task display
- TextField for task input
- Checkbox for task completion
- IconButton for task deletion

## Getting Started

1. Clone the repository
2. Install dependencies:
   ```bash
   flutter pub get
   ```
3. Run the app:
   ```bash
   flutter run
   ```

## API Usage

### Local Storage API
The app uses SharedPreferences to store tasks locally:

```dart
// Save tasks
Future<void> _saveTodos() async {
  final prefs = await SharedPreferences.getInstance();
  final todosJson = _todos.map((todo) => jsonEncode(todo.toJson())).toList();
  await prefs.setStringList('todos', todosJson);
}

// Load tasks
Future<void> _loadTodos() async {
  final prefs = await SharedPreferences.getInstance();
  final todosJson = prefs.getStringList('todos') ?? [];
  setState(() {
    _todos = todosJson.map((json) => Todo.fromJson(jsonDecode(json))).toList();
  });
}
```

## Features Implementation

### Adding Tasks
```dart
void _addTodo(String title) {
  if (title.trim().isEmpty) return;
  
  setState(() {
    _todos.add(Todo(
      id: DateTime.now().millisecondsSinceEpoch.toString(),
      title: title,
      isCompleted: false,
    ));
  });
  _saveTodos();
  _textController.clear();
}
```

### Completing Tasks
```dart
void _toggleTodo(String id) {
  setState(() {
    final todo = _todos.firstWhere((todo) => todo.id == id);
    todo.isCompleted = !todo.isCompleted;
  });
  _saveTodos();
}
```

### Deleting Tasks
```dart
void _deleteTodo(String id) {
  setState(() {
    _todos.removeWhere((todo) => todo.id == id);
  });
  _saveTodos();
}
```

## UI Implementation

The app's UI is built using Flutter's Material Design components:

```dart
Scaffold(
  appBar: AppBar(
    title: const Text('To-Do List'),
    backgroundColor: Theme.of(context).colorScheme.inversePrimary,
  ),
  body: Column(
    children: [
      // Task input field
      Padding(
        padding: const EdgeInsets.all(8.0),
        child: Row(
          children: [
            Expanded(
              child: TextField(
                controller: _textController,
                decoration: const InputDecoration(
                  hintText: 'Add a new task',
                  border: OutlineInputBorder(),
                ),
                onSubmitted: _addTodo,
              ),
            ),
            ElevatedButton(
              onPressed: () => _addTodo(_textController.text),
              child: const Text('Add'),
            ),
          ],
        ),
      ),
      // Task list
      Expanded(
        child: ListView.builder(
          itemCount: _todos.length,
          itemBuilder: (context, index) {
            final todo = _todos[index];
            return ListTile(
              leading: Checkbox(
                value: todo.isCompleted,
                onChanged: (_) => _toggleTodo(todo.id),
              ),
              title: Text(
                todo.title,
                style: TextStyle(
                  decoration: todo.isCompleted ? TextDecoration.lineThrough : null,
                ),
              ),
              trailing: IconButton(
                icon: const Icon(Icons.delete),
                onPressed: () => _deleteTodo(todo.id),
              ),
            );
          },
        ),
      ),
    ],
  ),
)
```

## Future Improvements

- Add task categories
- Implement task priorities
- Add due dates
- Add task search functionality
- Implement task sorting
- Add dark mode support
- Add task sharing capability
- Implement task backup/restore
