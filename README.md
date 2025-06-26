import 'dart:typed_data';
import 'package:flutter/material.dart';
import 'package:flutter/services.dart' show rootBundle;

void main() => runApp(AviAnimatorApp());

class AviAnimatorApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Avi Animator',
      theme: ThemeData(primarySwatch: Colors.deepPurple),
      home: AnimationHome(),
    );
  }
}

class AnimationHome extends StatefulWidget {
  @override
  _AnimationHomeState createState() => _AnimationHomeState();
}

class _AnimationHomeState extends State<AnimationHome> {
  final _storyController = TextEditingController();
  String selectedStyle = artStyles.keys.first;
  List<Uint8List> frames = [];

  void _generateFrames() async {
    final scenes = _storyController.text
        .split(RegExp(r'\n\s*\n'))
        .map((s) => s.trim())
        .where((s) => s.isNotEmpty)
        .toList();

    final newFrames = <Uint8List>[];
    for (var scene in scenes) {
      final placeholder = await rootBundle.load('assets/placeholder.png');
      newFrames.add(placeholder.buffer.asUint8List());
    }

    setState(() => frames = newFrames);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Avi Animator')),
      body: Padding(
        padding: const EdgeInsets.all(14),
        child: Column(
          children: [
            DropdownButton<String>(
              value: selectedStyle,
              onChanged: (v) => setState(() => selectedStyle = v!),
              items: artStyles.keys
                  .map((style) => DropdownMenuItem(
                        value: style,
                        child: Text(style),
                      ))
                  .toList(),
            ),
            TextField(
              controller: _storyController,
              maxLines: 5,
              decoration: InputDecoration(hintText: "Enter your story scenes..."),
            ),
            ElevatedButton(
              onPressed: _generateFrames,
              child: Text("Generate Frames"),
            ),
            Expanded(
              child: ListView.builder(
                itemCount: frames.length,
                itemBuilder: (context, i) => Padding(
                  padding: const EdgeInsets.symmetric(vertical: 8),
                  child: Image.memory(frames[i]),
                ),
              ),
            )
          ],
        ),
      ),
    );
  }
}

const Map<String, String> artStyles = {
  "Studio Ghibli": "soft watercolor, cinematic light",
  "Cyberpunk": "neon cityscape, dark shadows",
  "Modern Anime": "cel-shading, vivid eyes",
  "Charcoal Sketch": "monochrome, rough lines",
};
