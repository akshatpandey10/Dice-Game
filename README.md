# Dice-Game

//this is my first time coding anything, plus the playlist was not even close to being enough.
//however i used a bit of gpt and understood every line of code, got familiar with the syntax, and have gained some programming aptitude too.

import 'package:flutter/material.dart';
import 'dart:math';

void main() => runApp(
  MaterialApp(
    home: const RandomisedNumber(),
  ),
);

class RandomisedNumber extends StatefulWidget {
  const RandomisedNumber({super.key});

  @override
  State<RandomisedNumber> createState() => _RandomisedNumberState();
}

class _RandomisedNumberState extends State<RandomisedNumber> {
  int walletBalance = 10;
  int randomNumber1 = 1;
  int randomNumber2 = 1;
  int randomNumber3 = 1;
  int randomNumber4 = 1;
  String userInput = "";
  final TextEditingController _controller = TextEditingController();

  List<String> diceImages = [
    'https://static.vecteezy.com/system/resources/previews/000/365/909/non_2x/dice-one-vector-icon.jpg',
    'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSvfYHHhti8ZL-ng3OWSDrYtGNyCsRSSQEBdg&s',
    'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRvl6yx-Nqmyk_S3YMsFYCQj8fHYGZolK00BOtL0dffHrUSXmLpn0ecmJ6HI0EQWTYv9Do&usqp=CAU',
    'https://cdn3.iconfinder.com/data/icons/block/32/dice_four-512.png',
    'https://cdn.icon-icons.com/icons2/2717/PNG/512/dice_five_icon_173877.png',
    'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTM0903q7-_tmVwFVYTi-xTxu6RZA6AC2IQ9A&s',
  ];

  void rollDice(String gameMode) {
    int wager = int.tryParse(userInput) ?? 0;
    int multiplier = 0;

    switch (gameMode) {
      case '2 Alike':
        multiplier = 2;
        break;
      case '3 Alike':
        multiplier = 3;
        break;
      case '4 Alike':
        multiplier = 4;
        break;
    }

    if (wager <= 0 || wager > walletBalance ~/ multiplier) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(
          content: Text('Invalid wager! Max wager for "$gameMode" is ${walletBalance ~/ multiplier}'),
        ),
      );
      return;
    }

    setState(() {
      randomNumber1 = Random().nextInt(6) + 1;
      randomNumber2 = Random().nextInt(6) + 1;
      randomNumber3 = Random().nextInt(6) + 1;
      randomNumber4 = Random().nextInt(6) + 1;

      List<int> rolls = [randomNumber1, randomNumber2, randomNumber3, randomNumber4];
      Map<int, int> rollCounts = {};

      for (var roll in rolls) {
        rollCounts[roll] = (rollCounts[roll] ?? 0) + 1;
      }

      bool won = false;
      if (multiplier == 2 && rollCounts.containsValue(2)) {
        won = true;
      } else if (multiplier == 3 && rollCounts.containsValue(3)) {
        won = true;
      } else if (multiplier == 4 && rollCounts.containsValue(4)) {
        won = true;
      }

      if (won) {
        walletBalance += wager * multiplier;
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(
            content: Text(
              'You won! Rolls: $rolls. Wallet updated to $walletBalance.',
            ),
          ),
        );
      } else {
        walletBalance -= wager * multiplier;
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(
            content: Text(
              'You lost! Rolls: $rolls. Wallet updated to $walletBalance.',
            ),
          ),
        );
      }

      _controller.clear();
    });
  }

  void resetWager() {
    setState(() {
      userInput = "";
      _controller.clear();
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text(
          'Dice Game',
          style: TextStyle(fontSize: 24),
        ),
        centerTitle: true,
        backgroundColor: Colors.amber,
      ),
      body: Stack(
        children: [
          Container(
            color: Colors.deepPurple,
          ),
          Center(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.spaceEvenly,
              children: <Widget>[
                Text(
                  'Wallet Balance: $walletBalance',
                  style: const TextStyle(fontSize: 36, color: Colors.amber),
                ),
                const Text(
                  'Place Wager',
                  style: TextStyle(fontSize: 48, color: Colors.amber),
                ),
                TextField(
                  controller: _controller,
                  onChanged: (value) {
                    setState(() {
                      userInput = value;
                    });
                  },
                  style: const TextStyle(color: Colors.amber, fontSize: 36),
                  decoration: const InputDecoration(
                    labelText: 'Your Wager',
                    hintText: 'Type your wager...',
                    border: OutlineInputBorder(),
                    labelStyle: TextStyle(color: Colors.amber),
                    hintStyle: TextStyle(color: Colors.amber),
                  ),
                  keyboardType: TextInputType.number,
                ),
                const Text(
                  'Choose Game Mode:',
                  style: TextStyle(fontSize: 36, color: Colors.amber),
                ),
                Row(
                  mainAxisAlignment: MainAxisAlignment.spaceEvenly,
                  children: [
                    ElevatedButton(
                      onPressed: () => rollDice('2 Alike'),
                      child: const Text('2 Alike'),
                    ),
                    ElevatedButton(
                      onPressed: () => rollDice('3 Alike'),
                      child: const Text('3 Alike'),
                    ),
                    ElevatedButton(
                      onPressed: () => rollDice('4 Alike'),
                      child: const Text('4 Alike'),
                    ),
                  ],
                ),
                Row(
                  mainAxisAlignment: MainAxisAlignment.spaceEvenly,
                  children: [
                    // Dice 1
                    Container(
                      color: Colors.amber,
                      padding: const EdgeInsets.all(20.0),
                      height: 120.0,
                      width: 90.0,
                      child: Image.network(
                        diceImages[randomNumber1 - 1],
                        fit: BoxFit.contain,
                      ),
                    ),
                    // Dice 2
                    Container(
                      color: Colors.amber,
                      padding: const EdgeInsets.all(20.0),
                      height: 120.0,
                      width: 90.0,
                      child: Image.network(
                        diceImages[randomNumber2 - 1],
                        fit: BoxFit.contain,
                      ),
                    ),
                    // Dice 3
                    Container(
                      color: Colors.amber,
                      padding: const EdgeInsets.all(20.0),
                      height: 120.0,
                      width: 90.0,
                      child: Image.network(
                        diceImages[randomNumber3 - 1],
                        fit: BoxFit.contain,
                      ),
                    ),
                    // Dice 4
                    Container(
                      color: Colors.amber,
                      padding: const EdgeInsets.all(20.0),
                      height: 120.0,
                      width: 90.0,
                      child: Image.network(
                        diceImages[randomNumber4 - 1],
                        fit: BoxFit.contain,
                      ),
                    ),
                  ],
                ),
                SizedBox(
                  width: 500.0,
                  height: 75.0,
                  child: ElevatedButton(
                    onPressed: () => rollDice('2 Alike'), // Default mode
                    child: const Text(
                      'Roll a Dice. Your last chosen wager and game mode is used.',
                      style: TextStyle(fontSize: 24),
                    ),
                  ),
                ),
                SizedBox(
                  width: 200.0,
                  height: 75.0,
                  child: ElevatedButton(
                    onPressed: resetWager,
                    child: const Text(
                      'Reset Wager',
                      style: TextStyle(fontSize: 24),
                    ),
                  ),
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }
}
