# Iron_discipline
It is an user friendly habit tracking and learning game app which develops your  discipline and word skills 
Discipline


























iron_discipline/

lib/
 main.dart

 models/
  habit.dart

 services/
  database_service.dart
  reward_service.dart

 screens/
  home_screen.dart
  habit_screen.dart
  game_screen.dart
  settings_screen.dart

 widgets/
  floating_letter.dart

 assets/
  dictionary.txt
  logo.pngname: iron_discipline
description: Habit tracker + word game discipline app

environment:
  sdk: ">=3.0.0"

dependencies:
  flutter:
    sdk: flutter

  sqflite: ^2.3.0
  path: ^1.8.3
  provider: ^6.0.5
  intl: ^0.18.1

flutter:
  uses-material-design: true

  assets:
    - assets/dictionary.txtimport 'package:flutter/material.dart';
import 'screens/home_screen.dart';

void main() {
  runApp(IronDisciplineApp());
}

class IronDisciplineApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Iron Discipline",
      theme: ThemeData.dark(),
      home: HomeScreen(),
    );
  }
}
class Habit {

  int? id;
  String name;
  bool completed;

  Habit({
    this.id,
    required this.name,
    this.completed = false
  });

}
import 'package:sqflite/sqflite.dart';
import 'package:path/path.dart';

class DatabaseService {

  static final DatabaseService instance = DatabaseService._init();
  static Database? _database;

  DatabaseService._init();

  Future<Database> get database async {

    if (_database != null) return _database!;

    _database = await _initDB("habits.db");

    return _database!;
  }

  Future<Database> _initDB(String filePath) async {

    final dbPath = await getDatabasesPath();
    final path = join(dbPath, filePath);

    return await openDatabase(
      path,
      version: 1,
      onCreate: _createDB,
    );
  }

  Future _createDB(Database db, int version) async {

    await db.execute('''
CREATE TABLE habits(
id INTEGER PRIMARY KEY AUTOINCREMENT,
name TEXT,
completed INTEGER
)
''');

  }
}
class RewardService {

  int coins = 0;

  int calculateReward(int completed, int total){

    double percent = completed / total;

    if(percent == 1){
      coins += 100;
    }
    else if(percent >= 0.9){
      coins += 70;
    }
    else if(percent >= 0.7){
      coins += 40;
    }
    else if(percent >= 0.5){
      coins += 20;
    }

    return coins;
  }

}
import 'package:flutter/material.dart';
import 'habit_screen.dart';
import 'game_screen.dart';
import 'settings_screen.dart';

class HomeScreen extends StatelessWidget {

  @override
  Widget build(BuildContext context) {

    return Scaffold(
      appBar: AppBar(
        title: Text("Iron Discipline"),
      ),

      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [

            ElevatedButton(
              child: Text("Habit Tracker"),
              onPressed: (){
                Navigator.push(context,
                MaterialPageRoute(builder: (_) => HabitScreen()));
              },
            ),

            ElevatedButton(
              child: Text("Word Game"),
              onPressed: (){
                Navigator.push(context,
                MaterialPageRoute(builder: (_) => GameScreen()));
              },
            ),

            ElevatedButton(
              child: Text("Settings"),
              onPressed: (){
                Navigator.push(context,
                MaterialPageRoute(builder: (_) => SettingsScreen()));
              },
            ),

          ],
        ),
      ),
    );
  }
}
import 'package:flutter/material.dart';

class HabitScreen extends StatefulWidget {
  @override
  _HabitScreenState createState() => _HabitScreenState();
}

class _HabitScreenState extends State<HabitScreen> {

  List<Map> habits = [

    {"name":"Wake Up Early", "done":false},
    {"name":"Morning Cardio", "done":false},
    {"name":"No Smoking", "done":false},
    {"name":"Cold Shower", "done":false},
    {"name":"Deep Work", "done":false},

  ];

  @override
  Widget build(BuildContext context) {

    return Scaffold(

      appBar: AppBar(title: Text("Habit Tracker")),

      body: ListView.builder(

        itemCount: habits.length,

        itemBuilder: (context,index){

          return CheckboxListTile(

            title: Text(habits[index]["name"]),

            value: habits[index]["done"],

            onChanged: (value){

              setState(() {

                habits[index]["done"] = value;

              });

            },
          );
        },
      ),
    );
  }
}
import 'dart:math';
import 'package:flutter/material.dart';

class GameScreen extends StatefulWidget {

  @override
  _GameScreenState createState() => _GameScreenState();

}

class _GameScreenState extends State<GameScreen> {

  String currentWord = "";
  int score = 0;

  List letters = ["A","B","C","D","E","F","G","H"];

  void tapLetter(String letter){

    setState(() {

      currentWord += letter;

      if(currentWord.length >= 3){

        score += currentWord.length * 5;

      }

    });

  }

  @override
  Widget build(BuildContext context){

    return Scaffold(

      appBar: AppBar(title: Text("Word Game")),

      body: Column(

        children: [

          Text("Word: $currentWord"),
          Text("Score: $score"),

          Wrap(

            children: letters.map((l){

              return GestureDetector(

                onTap: ()=>tapLetter(l),

                child: Container(

                  margin: EdgeInsets.all(8),

                  padding: EdgeInsets.all(20),

                  color: Colors.blue,

                  child: Text(l, style: TextStyle(fontSize: 24)),

                ),
              );

            }).toList(),

          )

        ],
      ),
    );
  }
}
import 'package:flutter/material.dart';

class SettingsScreen extends StatefulWidget {

  @override
  _SettingsScreenState createState() => _SettingsScreenState();

}

class _SettingsScreenState extends State<SettingsScreen> {

  String calendar = "Gregorian";

  List options = [
    "Gregorian",
    "Hindu",
    "Islamic",
    "Hebrew",
    "Buddhist",
    "Chinese",
    "Persian"
  ];

  @override
  Widget build(BuildContext context){

    return Scaffold(

      appBar: AppBar(title: Text("Calendar Type")),

      body: ListView(

        children: options.map((c){

          return RadioListTile(

            title: Text(c),

            value: c,

            groupValue: calendar,

            onChanged: (value){

              setState(() {

                calendar = value.toString();

              });

            },

          );

        }).toList(),

      ),
    );
  }
}


