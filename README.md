import 'dart:async';
import 'package:flutter/material.dart';

void main() => runApp(MaterialApp(
      debugShowCheckedModeBanner: false,
      theme: ThemeData(primarySwatch: Colors.deepPurple),
      home: DashboardScreen(),
    ));

// --- DATA MODEL FOR WHATSAPP STYLE CHAT (DAY 3) ---
class ChatMessage {
  String text;
  bool isUser; // true = User (Right side), false = AI (Left side)
  ChatMessage({required this.text, required this.isUser});
}

// ==========================================================
// 📅 DAY 1: DASHBOARD SCREEN (PRICING & CUSTOM SCENARIO)
// ==========================================================
class DashboardScreen extends StatefulWidget {
  @override
  _DashboardScreenState createState() => _DashboardScreenState();
}

class _DashboardScreenState extends State<DashboardScreen> {
  int selectedTime = 15; // Default choice 15 mins
  final TextEditingController scenarioController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.white,
      appBar: AppBar(
        title: Text("English Coach", style: TextStyle(color: Colors.white, fontWeight: FontWeight.bold)),
        backgroundColor: Colors.deepPurple,
        elevation: 0,
      ),
      body: Padding(
        padding: EdgeInsets.all(20),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text("SELECT DURATION", style: TextStyle(fontSize: 14, fontWeight: FontWeight.bold, color: Colors.grey.shade600)),
            SizedBox(height: 15),
            
            // 4 Price Cards Grid Layout
            GridView.count(
              shrinkWrap: true,
              physics: NeverScrollableScrollPhysics(),
              crossAxisCount: 2,
              childAspectRatio: 1.4,
              crossAxisSpacing: 15,
              mainAxisSpacing: 15,
              children: [
                _buildTimeCard("Express", 15, "\$5.00"),
                _buildTimeCard("Power", 30, "\$10.00"),
                _buildTimeCard("Deep Dive", 45, "\$15.00"),
                _buildTimeCard("Mastery", 60, "\$20.00"),
              ],
            ),
            SizedBox(height: 30),

            Text("FOCUS AREA", style: TextStyle(fontSize: 14, fontWeight: FontWeight.bold, color: Colors.grey.shade600)),
            SizedBox(height: 10),
            
            // Custom dialog input text box
            TextField(
              controller: scenarioController,
              maxLines: 3,
              decoration: InputDecoration(
                labelText: "Custom Dialog Scenario",
                labelStyle: TextStyle(color: Colors.deepPurple),
                focusedBorder: OutlineInputBorder(
                  borderRadius: BorderRadius.circular(12),
                  borderSide: BorderSide(color: Colors.deepPurple, width: 2),
                ),
                border: OutlineInputBorder(borderRadius: BorderRadius.circular(12)),
                hintText: "E.g., Asking for directions at a busy airport terminal...",
              ),
            ),
            Spacer(),
            
            // Session Start Button
            SizedBox(
              width: double.infinity,
              height: 55,
              child: ElevatedButton(
                style: ElevatedButton.styleFrom(
                  backgroundColor: Colors.deepPurple,
                  shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(30)),
                ),
                child: Text("Start Coaching Session >>", style: TextStyle(fontSize: 18, color: Colors.white, fontWeight: FontWeight.bold)),
                onPressed: () {
                  Navigator.push(
                    context,
                    MaterialPageRoute(
                      builder: (context) => ChatScreen(
                        selectedMinutes: selectedTime,
                        scenario: scenarioController.text.isEmpty ? "General Conversation" : scenarioController.text,
                      ),
                    ),
                  );
                },
              ),
            ),
          ],
        ),
      ),
    );
  }

  // Helper widget to design pricing cards dynamically
  Widget _buildTimeCard(String title, int mins, String price) {
    bool isSelected = selectedTime == mins;
    return GestureDetector(
      onTap: () => setState(() => selectedTime = mins),
      child: Container(
        padding: EdgeInsets.all(15),
        decoration: BoxDecoration(
          color: isSelected ? Colors.deepPurple : Colors.grey.shade50,
          borderRadius: BorderRadius.circular(15),
          border: Border.all(color: isSelected ? Colors.deepPurple : Colors.grey.shade300, width: 2),
          boxShadow: isSelected ? [BoxShadow(color: Colors.purple.withOpacity(0.2), blurRadius: 6, offset: Offset(0, 3))] : [],
        ),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(title, style: TextStyle(color: isSelected ? Colors.white70 : Colors.grey.shade600, fontSize: 12, fontWeight: FontWeight.w500)),
            SizedBox(height: 4),
            Text("$mins mins", style: TextStyle(color: isSelected ? Colors.white : Colors.black87, fontSize: 20, fontWeight: FontWeight.bold)),
            SizedBox(height: 8),
            Container(
              padding: EdgeInsets.symmetric(horizontal: 10, vertical: 4),
              decoration: BoxDecoration(color: isSelected ? Colors.mintAccent.shade400 : Colors.green.shade500, borderRadius: BorderRadius.circular(8)),
              child: Text(price, style: TextStyle(color: Colors.white, fontSize: 12, fontWeight: FontWeight.bold)),
            )
          ],
        ),
      ),
    );
  }
}

// ==========================================================
// 📅 DAY 2 & 3: COACHING CHAT SCREEN WITH LOCKING LOGIC
// ==========================================================
class ChatScreen extends StatefulWidget {
  final int selectedMinutes;
  final String scenario;
  ChatScreen({required this.selectedMinutes, required this.scenario});

  @override
  _ChatScreenState createState() => _ChatScreenState();
}

class _ChatScreenState extends State<ChatScreen> {
  late int _remainingSeconds;
  Timer? _timer;
  bool _isTimerRunning = false;
  String _statusMessage = "Bolnay ya likhnay ke liye pehle START button dabayein.";
  
  // Chat list initiated with welcome message from AI Coach
  List<ChatMessage> chatMessages = [
    ChatMessage(text: "Hello! Welcome to your coaching session. Press START to begin speaking.", isUser: false)
  ];
  
  final TextEditingController _textController = TextEditingController();
  final ScrollController _scrollController = ScrollController();

  @override
  void initState() {
    super.initState();
    _remainingSeconds = widget.selectedMinutes * 60; // Convert user selection to seconds
  }

  // Day 2 Timer Logic with locking conditions
  void _toggleTimer() {
    if (_isTimerRunning) {
      _timer?.cancel();
      setState(() {
        _isTimerRunning = false;
        _statusMessage = "Class PAUSED! Chat aur mic block ho chuka hai.";
      });
    } else {
      setState(() {
        _isTimerRunning = true;
        _statusMessage = "AI sun raha hai... Context: '${widget.scenario}'";
      });
      _timer = Timer.periodic(Duration(seconds: 1), (timer) {
        if (_remainingSeconds > 0) {
          setState(() => _remainingSeconds--);
        } else {
          _timer?.cancel();
          _endClass();
        }
      });
    }
  }

  // Day 3 Text input management with automated dummy response simulation
  void _sendMessage() {
    if (_textController.text.trim().isEmpty) return;
    setState(() {
      chatMessages.add(ChatMessage(text: _textController.text, isUser: true));
      _textController.clear();
    });
    
    // Auto scroll down to view new bubble
    _scrollToBottom();
    
    // Simulate AI thinking and replying after 1.5 seconds
    Timer(Duration(milliseconds: 1500), () {
      if (mounted) {
        setState(() {
          chatMessages.add(ChatMessage(text: "That is correct! Let's continue practicing our conversation context.", isUser: false));
        });
        _scrollToBottom();
      }
    });
  }

  void _scrollToBottom() {
    Timer(Duration(milliseconds: 100), () {
      if (_scrollController.hasClients) {
        _scrollController.animateTo(
          _scrollController.position.maxScrollExtent,
          duration: Duration(milliseconds: 300),
          curve: Curves.easeOut,
        );
      }
    });
  }

  void _endClass() {
    setState(() {
      _isTimerRunning = false;
      _statusMessage = "Time Over! Aaj ki class khatam.";
    });
  }

  @override
  void dispose() {
    _timer?.cancel();
    _textController.dispose();
    _scrollController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.grey.shade100,
      appBar: AppBar(
        backgroundColor: Colors.deepPurple,
        leading: IconButton(
          icon: Icon(Icons.arrow_back, color: Colors.white),
          onPressed: () => Navigator.pop(context), // Day 2: Back button control
        ),
        title: Text("Session: ${widget.selectedMinutes} mins", style: TextStyle(color: Colors.white, fontSize: 16, fontWeight: FontWeight.bold)),
        actions: [
          Center(
            child: Padding(
              padding: EdgeInsets.only(right: 20),
              child: Text(
                '${(_remainingSeconds ~/ 60).toString().padLeft(2, '0')}:${(_remainingSeconds % 60).toString().padLeft(2, '0')}',
                style: TextStyle(fontSize: 22, fontWeight: FontWeight.bold, color: Colors.greenAccent),
              ),
            ),
          )
        ],
      ),
      body: Column(
        children: [
          // Warning indicator block text
          Container(
