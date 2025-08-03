<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8" />
  <title>Daily Task Creator</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <link rel="manifest" href="manifest.json" />
  <script src="main.dart.js" defer></script>
</head>
<body>
  <script>
    // This script loads the Flutter app.
  </script>
</body>
</html>
import 'package:flutter/material.dart';
import 'screens/home_screen.dart';

void main() => runApp(DailyTaskCreator());

class DailyTaskCreator extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Daily Task Creator',
      theme: ThemeData.dark(),
      home: HomeScreen(),
    );
  }
}
import 'package:flutter/material.dart';
import '../models/task.dart';

class HomeScreen extends StatefulWidget {
  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  List<Task> tasks = [];
  int xp = 0;

  final _nameCtrl = TextEditingController();
  final _durationCtrl = TextEditingController();
  final _priorityCtrl = TextEditingController();
  final _difficultyCtrl = TextEditingController();
  final _xpCtrl = TextEditingController();

  void _addTask() {
    String name = _nameCtrl.text.trim();
    int? duration = int.tryParse(_durationCtrl.text);
    int? priority = int.tryParse(_priorityCtrl.text);
    int? difficulty = int.tryParse(_difficultyCtrl.text);

    if (name.isEmpty || duration == null || priority == null || difficulty == null) {
      return;
    }

    setState(() {
      tasks.add(Task(
          name: name,
          duration: duration,
          priority: priority,
          difficulty: difficulty,
          completed: false));
      _nameCtrl.clear();
      _durationCtrl.clear();
      _priorityCtrl.clear();
      _difficultyCtrl.clear();
    });
  }

  void _addXP() {
    int? added = int.tryParse(_xpCtrl.text);
    if (added == null) return;
    setState(() {
      xp += added;
      _xpCtrl.clear();
    });
  }

  void _toggleComplete(int index) {
    setState(() {
      tasks[index].completed = !tasks[index].completed;
    });
  }

  void _clearCompleted() {
    setState(() {
      tasks.removeWhere((task) => task.completed);
    });
  }

  @override
  Widget build(BuildContext context) {
    List<Task> activeTasks = tasks.where((t) => !t.completed).toList();
    activeTasks.sort((a, b) {
      int c = a.priority.compareTo(b.priority);
      return c != 0 ? c : b.difficulty.compareTo(a.difficulty);
    });

    return Scaffold(
      appBar: AppBar(
        title: Text("Daily Task Creator"),
        actions: [
          IconButton(icon: Icon(Icons.delete), onPressed: _clearCompleted)
        ],
      ),
      body: SingleChildScrollView(
        padding: EdgeInsets.all(16),
        child: Column(
          children: [
            Text("XP: $xp", style: TextStyle(fontSize: 24)),
            TextField(
              controller: _nameCtrl,
              decoration: InputDecoration(labelText: "Task Name"),
            ),
            TextField(
              controller: _durationCtrl,
              decoration: InputDecoration(labelText: "Duration (min)"),
              keyboardType: TextInputType.number,
            ),
            TextField(
              controller: _priorityCtrl,
              decoration: InputDecoration(labelText: "Priority (1=High)"),
              keyboardType: TextInputType.number,
            ),
            TextField(
              controller: _difficultyCtrl,
              decoration: InputDecoration(labelText: "Difficulty (1-5)"),
              keyboardType: TextInputType.number,
            ),
            SizedBox(height: 10),
            ElevatedButton(onPressed: _addTask, child: Text("Add Task")),
            SizedBox(height: 20),
            TextField(
              controller: _xpCtrl,
              decoration: InputDecoration(labelText: "Add XP"),
              keyboardType: TextInputType.number,
            ),
            ElevatedButton(onPressed: _addXP, child: Text("Add XP")),
            SizedBox(height: 20),
            ListView.builder(
              shrinkWrap: true,
              physics: NeverScrollableScrollPhysics(),
              itemCount: activeTasks.length,
              itemBuilder: (context, i) {
                var task = activeTasks[i];
                return ListTile(
                  title: Text(task.name),
                  subtitle: Text(
                      "Duration: ${task.duration} min | Priority: ${task.priority} | Difficulty: ${task.difficulty}"),
                  trailing: Checkbox(
                    value: task.completed,
                    onChanged: (val) => _toggleComplete(i),
                  ),
                );
              },
            ),
          ],
        ),
      ),
    );
  }
}
