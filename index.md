## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/cm710719/word-finder/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/cm710719/word-finder/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.

// wordFinder.cpp
// Author: Colleen McGuire
// 5 February 2022

#include <iostream>
#include <fstream>
#include <string>
#include <vector>
#include <cctype>

using namespace std;
int menu();
int words_by_letter_count(size_t n, string filename);
int words_with_letter(char letter, string filename);
int words_with_letter_series(string letters, string filename);
int words_with_letter_at_position(char letter, int pos, string filename);
int words_without_letter(char letter, string filename);
int words_with_letter_not_at_position(char letter, int pos, string filename);

int main(){
    int search_count = 0;
    int choice;
    int word_count;
    string filename;
    
    do {
        choice = menu();
        cout << endl;
        if (search_count == 0)
            filename = "words.txt";
        else
            filename = "results.txt";
        switch(choice){
            case 1: 
                size_t n;
                cout << "Enter number of letters: ";
                cin >> n;

                word_count = words_by_letter_count(n, filename);
                cout << "There are " << word_count << " words possible" << endl;
                break;
            case 2:
                char letter;
                cout << "Enter letter: ";
                cin >> letter;
                
                word_count = words_with_letter(toupper(letter), filename);
                cout << "There are " << word_count << " words possible" << endl;
                break;
            case 3:{
                string letters;
                cout << "Enter series of letters without spaces: " << endl;
                cin >> letters;

                word_count = words_with_letter_series(letters, filename);
                cout << "There are " << word_count << " words possible" << endl;
                break;
            }
            case 4: {
                char letter;
                cout << "Enter letter: ";
                cin >> letter;

                int pos;
                cout << "Enter position, starting at 1: ";
                cin >> pos;

                word_count = words_with_letter_at_position(toupper(letter), pos, filename);
                cout << "There are " << word_count << " words possible" << endl;
                break;
            }
            case 5:{
                char letter;
                cout << "Enter letters to avoid, enter 0 once finished." << endl;
                do{
                    cout << "Enter letter to avoid: ";
                    cin >> letter;
                    word_count = words_without_letter(toupper(letter), filename);
                } while (letter != '0');

                cout << "There are " << word_count << " words possible" << endl;
                break;
            }
            case 6:{
                char letter;
                int pos;
                cout << "Enter letter: ";
                cin >> letter;
                cout << "Enter position to avoid, starting at 1: ";
                cin >> pos;

                word_count = words_with_letter_not_at_position(toupper(letter), pos, filename);
                cout << "There are " << word_count << " words possible" << endl;
                break;
            }
            case 7:{
                cout << "All possible words (" << word_count << "):\n" << endl;
                string word;
                ifstream ins;
                ins.open("results.txt");
                while(ins >> word)
                    cout << word << endl;
                cout << endl;
                break;
            }
            case 0:{
                cout << "Thank you, goodbye!";
                break;
            }
            default:
                cout << "Invalid choice. Try again" << endl;
                break;  
        } 
        ++search_count;
    } while (choice != 0);
}

int menu() {

    int user_choice;
    
    cout << "\nMake a selection: " << endl;
    cout << "1 - Find words with n letters" << endl;
    cout << "2 - Find words containing letter x" << endl;
    cout << "3 - Find words containing series of letters xyz" << endl;
    cout << "4 - Find words with letter x at position n" << endl;
    cout << "5 - Find words without letter x" << endl;
    cout << "6 - Find words with letter x not at position n" << endl;
    cout << "7 - View all valid words" << endl;
    cout << "0 - Quit program" << endl;
    cout << "Select -> ";
    
    cin >> user_choice;
    return user_choice;
}

int words_by_letter_count(size_t n, string filename) {
    ifstream ins;
    ins.open(filename);
    
    int count = 0;
    string word;
    vector<string> words;

    while(ins >> word){
        if (word.length() == n) {
            words.push_back(word);
            ++count;
        }
    }
    ofstream outs("results.txt");
    for (vector<string>::iterator it = words.begin(); it != words.end(); ++it)
        outs << *it << endl;

    return count; 
}

int words_with_letter(char letter, string filename) {
    ifstream ins;
    ins.open(filename);

    int count = 0;
    string word;
    vector<string> words;

    while(ins >> word){
        for (size_t i = 0; i < word.length(); i++){
            if (word[i] == letter){
                words.push_back(word);
                ++count;
                break;
            }
        }
    }
    ofstream outs("results.txt");
    for (vector<string>::iterator it = words.begin(); it != words.end(); ++it)
        outs << *it << endl;
    
    return count;
}

int words_with_letter_series(string letters, string filename){
    ifstream ins;
    ins.open(filename);

    int count = 0;
    string word;
    vector <string> words;

    while(ins >> word){
        for (size_t i = 0; i < word.length(); ++i) {
            for (size_t j = 0; j < letters.length(); ++j){
                if (i + j < word.length() && word[i+j] == letters[j]){
                    if (j == letters.length()-1){
                        words.push_back(word);
                        ++count;
                    }
                }
                else
                    break;
            }    
        }
        
    }
    
    ofstream outs("results.txt");
    for (vector<string>::iterator it = words.begin(); it != words.end(); ++it)
        outs << *it << endl;

    return count;
}

int words_with_letter_at_position(char letter, int pos, string filename){
    ifstream ins;
    ins.open(filename);

    int count = 0;
    string word;
    vector <string> words;

    while(ins >> word){
        if (word[pos-1] == letter){
            words.push_back(word);
            ++count;
        }
    }

    ofstream outs("results.txt");
    for (vector<string>::iterator it = words.begin(); it != words.end(); ++it)
        outs << *it << endl;

    return count;
}

int words_without_letter(char letter, string filename){
    ifstream ins;
    ins.open(filename);

    int count = 0;
    string word;
    vector <string> words;
    
    while(ins >> word){
        for (size_t i = 0; i < word.length(); i++){
            if (word[i] == letter)
                break;
            if (i == word.length() - 1){
                words.push_back(word);
                ++count;
            }
        }
    }

    ofstream outs("results.txt");
    for (vector<string>::iterator it = words.begin(); it != words.end(); ++it)
        outs << *it << endl;
    
    return count;
}

int words_with_letter_not_at_position(char letter, int pos, string filename){
    ifstream ins;
    ins.open(filename);

    bool contains;
    int count = 0;
    string word;
    vector<string> words;

    while(ins >> word){
        contains = false;
        for (size_t i = 0; i < word.length(); ++i){
            if (word[i] == letter){
                contains = true;
                break;
            }
        }
        if (tolower(word[pos-1]) != letter && contains == true){
            words.push_back(word);
            ++count;
        }
    }

    ofstream outs("results.txt");
    for (vector<string>::iterator it = words.begin(); it != words.end(); ++it)
        outs << *it << endl;

    return count;
}
