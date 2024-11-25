#include <iostream>
#include <stack>
#include <queue>
#include <string>

using namespace std;

class TextEditor {
private:
    string text; // Using a string to store characters as an array
    stack<string> undoStack; // Stack for undo operations
    stack<string> redoStack; // Stack for redo operations
    queue<string> clipboardQueue; // Queue for clipboard management

public:
    // Insert text at a specified position
    void insertText(int position, const string& newText) {
        if (position < 0 || position > text.length()) {
            cout << "Invalid position!" << endl;
            return;
        }
        // Save current state for undo
        undoStack.push(text);
        while (!redoStack.empty()) redoStack.pop(); // Clear redo stack
        text.insert(position, newText);
    }

    // Delete text starting from a specified position and with a specified length
    void deleteText(int position, int length) {
        if (position < 0 || position >= text.length() || length < 0) {
            cout << "Invalid position or length!" << endl;
            return;
        }
        // Save current state for undo
        undoStack.push(text);
        while (!redoStack.empty()) redoStack.pop(); // Clear redo stack
        text.erase(position, length);
    }

    // Copy text to the clipboard (using queue)
    void copy(int position, int length) {
        if (position < 0 || position >= text.length() || length < 0 || position + length > text.length()) {
            cout << "Invalid copy operation!" << endl;
            return;
        }
        string copiedText = text.substr(position, length);
        clipboardQueue.push(copiedText);
    }

    // Paste text from the clipboard at a specified position
    void paste(int position) {
        if (clipboardQueue.empty()) {
            cout << "Clipboard is empty!" << endl;
            return;
        }
        if (position < 0 || position > text.length()) {
            cout << "Invalid position!" << endl;
            return;
        }
        // Save current state for undo
        undoStack.push(text);
        while (!redoStack.empty()) redoStack.pop(); // Clear redo stack
        string pastedText = clipboardQueue.front();
        text.insert(position, pastedText);
    }

    // Undo the last operation
    void undo() {
        if (undoStack.empty()) {
            cout << "Nothing to undo!" << endl;
            return;
        }
        // Save current state for redo
        redoStack.push(text);
        // Revert to the previous state
        text = undoStack.top();
        undoStack.pop();
    }

    // Redo the last undone operation
    void redo() {
        if (redoStack.empty()) {
            cout << "Nothing to redo!" << endl;
            return;
        }
        // Save current state for undo
        undoStack.push(text);
        // Reapply the last undone state
        text = redoStack.top();
        redoStack.pop();
    }

    // Display the current text
    void displayText() const {
        cout << "Current Text: " << text << endl;
    }
};

// Main function to demonstrate functionality
int main() {
    TextEditor editor;

    editor.insertText(0, "Hello World");
    editor.displayText(); // Output: "Hello World"

    editor.insertText(5, " Beautiful");
    editor.displayText(); // Output: "Hello Beautiful World"

    editor.deleteText(6, 10);
    editor.displayText(); // Output: "Hello World"

    editor.copy(0, 5); // Copy "Hello"
    editor.paste(11);
    editor.displayText(); // Output: "Hello WorldHello"

    editor.undo();
    editor.displayText(); // Output: "Hello World"

    editor.redo();
    editor.displayText(); // Output: "Hello WorldHello"

    return 0;
}
