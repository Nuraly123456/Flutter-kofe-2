# lllllll3

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'dart:convert';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Coffee Order',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: CoffeeOrderPage(),
    );
  }
}

class CoffeeOrderPage extends StatefulWidget {
  @override
  _CoffeeOrderPageState createState() => _CoffeeOrderPageState();
}

class _CoffeeOrderPageState extends State<CoffeeOrderPage> {
  String coffeeType = 'espresso';
  bool addMilk = false;
  int sugar = 0;
  String totalPrice = '';

  Future<void> orderCoffee() async {
    var url = Uri.parse('http://127.0.0.1:5002/order'); // Flask серверінің URL-ін қосыңыз
    var response = await http.post(url,
        headers: {'Content-Type': 'application/json'},
        body: json.encode({
          'coffee': coffeeType,
          'milk': addMilk,
          'sugar': sugar,
        }));

    if (response.statusCode == 200) {
      var data = json.decode(response.body);
      setState(() {
        totalPrice = "Total Price: ${data['total_price']} KZT";
      });
    } else {
      setState(() {
        totalPrice = "Error: ${response.body}";
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Order Coffee'),
      ),
      body: Padding(
        padding: EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            DropdownButton<String>(
              value: coffeeType,
              onChanged: (String? newValue) {
                setState(() {
                  coffeeType = newValue!;
                });
              },
              items: menu.keys.map((String coffee) {
                return DropdownMenuItem<String>(
                  value: coffee,
                  child: Text(coffee),
                );
              }).toList(),
            ),
            SwitchListTile(
              title: Text("Add Milk"),
              value: addMilk,
              onChanged: (bool value) {
                setState(() {
                  addMilk = value;
                });
              },
            ),
            TextField(
              decoration: InputDecoration(labelText: 'Sugar (grams)'),
              keyboardType: TextInputType.number,
              onChanged: (value) {
                setState(() {
                  sugar = int.tryParse(value) ?? 0;
                });
              },
            ),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: orderCoffee,
              child: Text('Order Coffee'),
            ),
            SizedBox(height: 20),
            Text(totalPrice),
          ],
        ),
      ),
    );
  }
}

Map<String, int> get menu => {
  "espresso": 790,
  "americano": 890,
  "latte": 1190,
  "cappuccino": 1190,
  "mocha": 1390
};

# Flutter-kofe-2
