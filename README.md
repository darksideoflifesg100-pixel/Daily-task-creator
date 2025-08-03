import 'package:flutter/material.dart';
import 'screens/home_screen.dart';

void main() {
  runApp(DailyTaskCreatorApp());
}

class DailyTaskCreatorApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Daily Task Creator',
      theme: ThemeData(primarySwatch: Colors.indigo),
      home: HomeScreen(),
    );
  }
}
