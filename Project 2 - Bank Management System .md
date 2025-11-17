---
DATE:
DONE: true
project: 4
---

- Write a program to do the following : 
- 1 .<span style="color:rgb(255, 31, 31)"> Show Client List</span>.
- 2 . <span style="color:rgb(255, 31, 31)">Add New Client</span>.
- 3 . <span style="color:rgb(255, 31, 31)">Delete Client</span>.
- 4 . <span style="color:rgb(255, 31, 31)">Update Client Info</span>.
- 5 . <span style="color:rgb(255, 31, 31)">Find Client</span>.
- 6 . <span style="color:rgb(255, 31, 31)">Exit</span>. 

```cpp

#include <iostream>
#include <fstream>
#include <string>
#include <vector>
#include <iomanip>

using namespace std;

const string ClientsFileName = "Clients.txt";

void ShowMainMenue();

struct sClient
{
    string AccountNumber;
    string PinCode;
    string Name;
    string Phone;
    double AccountBalance;
    bool MarkForDelete = false;
};

vector<string> SplitString(string S1, string Delim)
{
    vector<string> vString;

    short position = 0;

    string sWord; // define a string variable  

    // use find() function to get the position of the delimiters  
    while ((position = S1.find(Delim)) != std::string::npos)
    {

        sWord = S1.substr(0, position); // store the word  

        if (sWord != "")
        {
            vString.push_back(sWord);
        }

        S1.erase(0, position + Delim.length());  /* erase() until positon and move to next word. */
    }

    if (S1 != "")
    {
        vString.push_back(S1); // it adds last word of the string.
    }

    return vString;

}

sClient ConvertLinetoRecord(string Line, string Seperator = "#")
{
    sClient Client;

    vector <string> vClientData;

    vClientData = SplitString(Line, Seperator);

    Client.AccountNumber = vClientData[0];
    Client.PinCode = vClientData[1];
    Client.Name = vClientData[2];
    Client.Phone = vClientData[3];
    Client.AccountBalance = stod(vClientData[4]);//cast string to double

    return Client;
}

string ConvertRecordToLine(sClient Client, string Seperator = "#")
{

    string stClientRecord = "";

    stClientRecord += Client.AccountNumber + Seperator;
    stClientRecord += Client.PinCode + Seperator;
    stClientRecord += Client.Name + Seperator;
    stClientRecord += Client.Phone + Seperator;
    stClientRecord += to_string(Client.AccountBalance);

    return stClientRecord;
}

bool ClientExistsByAccountNumber(string AccountNumber, string FileName)
{

    vector <sClient> vClients;

    fstream MyFile;

    MyFile.open(FileName, ios::in);//read Mode

    if (MyFile.is_open())
    {
        string Line;

        sClient Client;

        while (getline(MyFile, Line))
        {
            Client = ConvertLinetoRecord(Line);
            if (Client.AccountNumber == AccountNumber)
            {
                MyFile.close();
                return true;
            }
            vClients.push_back(Client);
        }

        MyFile.close();

    }
    return false;
}

sClient ReadNewClient()
{
    sClient Client;


    cout << "Enter Account Number? ";
    getline(cin >> ws, Client.AccountNumber); // Usage of std::ws will extract allthe whitespace character

    while (ClientExistsByAccountNumber(Client.AccountNumber, ClientsFileName))
    {
        cout << "\nClient with [" << Client.AccountNumber << "] already exists, Enter another Account Number? ";
        getline(cin >> ws, Client.AccountNumber);
    }

    cout << "Enter PinCode? ";
    getline(cin, Client.PinCode);

    cout << "Enter Name? ";
    getline(cin, Client.Name);

    cout << "Enter Phone? ";
    getline(cin, Client.Phone);

    cout << "Enter AccountBalance? ";
    cin >> Client.AccountBalance;

    return Client;
}

vector <sClient> LoadCleintsDataFromFile(string FileName)
{
    vector <sClient> vClients;

    fstream MyFile;

    MyFile.open(FileName, ios::in);//read Mode

    if (MyFile.is_open())
    {
        string Line;

        sClient Client;

        while (getline(MyFile, Line))
        {
            Client = ConvertLinetoRecord(Line);
            vClients.push_back(Client);
        }
        MyFile.close();
    }
    return vClients;
}

void PrintClientRecordLine(sClient Client)
{
    cout << "| " << setw(15) << left << Client.AccountNumber;
    cout << "| " << setw(10) << left << Client.PinCode;
    cout << "| " << setw(40) << left << Client.Name;
    cout << "| " << setw(12) << left << Client.Phone;
    cout << "| " << setw(12) << left << Client.AccountBalance;
}

void ShowAllClientsScreen()
{
    vector <sClient> vClients = LoadCleintsDataFromFile(ClientsFileName);

    cout << "\n\t\t\t\t\tClient List (" << vClients.size() << ") Client(s).";
    cout << "\n_______________________________________________________";
    cout << "_________________________________________\n" << endl;

    cout << "| " << left << setw(15) << "Accout Number";
    cout << "| " << left << setw(10) << "Pin Code";
    cout << "| " << left << setw(40) << "Client Name";
    cout << "| " << left << setw(12) << "Phone";
    cout << "| " << left << setw(12) << "Balance";
    cout << "\n_______________________________________________________";
    cout << "_________________________________________\n" << endl;

    if (vClients.size() == 0)
        cout << "\t\t\t\tNo Clients Available In the System!";
    else

        for (sClient& Client : vClients)
        {

            PrintClientRecordLine(Client);
            cout << endl;
        }

    cout << "\n_______________________________________________________";
    cout << "_________________________________________\n" << endl;
}

void PrintClientCard(sClient Client)
{
    cout << "\nThe following are the client details:\n";
    cout << "-----------------------------------";
    cout << "\nAccout Number: " << Client.AccountNumber;
    cout << "\nPin Code     : " << Client.PinCode;
    cout << "\nName         : " << Client.Name;
    cout << "\nPhone        : " << Client.Phone;
    cout << "\nAccount Balance: " << Client.AccountBalance;
    cout << "\n-----------------------------------\n";
}

bool FindClientByAccountNumber(string AccountNumber, vector <sClient> vClients, sClient& Client)
{
    for (sClient& C : vClients)
    {

        if (C.AccountNumber == AccountNumber)
        {
            Client = C;
            return true;
        }

    }
    return false;
}

sClient ChangeClientRecord(string AccountNumber)
{
    sClient Client;

    Client.AccountNumber = AccountNumber;

    cout << "\n\nEnter PinCode? ";
    getline(cin >> ws, Client.PinCode);

    cout << "Enter Name? ";
    getline(cin, Client.Name);

    cout << "Enter Phone? ";
    getline(cin, Client.Phone);

    cout << "Enter AccountBalance? ";
    cin >> Client.AccountBalance;

    return Client;
}

bool MarkClientForDeleteByAccountNumber(string AccountNumber, vector <sClient>& vClients)
{

    for (sClient& C : vClients)
    {

        if (C.AccountNumber == AccountNumber)
        {
            C.MarkForDelete = true;
            return true;
        }

    }

    return false;
}

vector <sClient> SaveCleintsDataToFile(string FileName, vector <sClient> vClients)
{
    fstream MyFile;

    MyFile.open(FileName, ios::out);//overwrite

    string DataLine;

    if (MyFile.is_open())
    {
        for (sClient C : vClients)
        {

            if (C.MarkForDelete == false)
            {
                //we only write records that are not marked for delete.  
                DataLine = ConvertRecordToLine(C);
                MyFile << DataLine << endl;
            }

        }

        MyFile.close();
    }

    return vClients;
}

void AddDataLineToFile(string FileName, string  stDataLine)
{
    fstream MyFile;

    MyFile.open(FileName, ios::out | ios::app);

    if (MyFile.is_open())
    {

        MyFile << stDataLine << endl;

        MyFile.close();
    }
}

void AddNewClient()
{
    sClient Client;

    Client = ReadNewClient();

    AddDataLineToFile(ClientsFileName, ConvertRecordToLine(Client));
}

void AddNewClients()
{
    char AddMore = 'Y';
    do
    {
        //system("cls");
        cout << "Adding New Client:\n\n";

        AddNewClient();
        cout << "\nClient Added Successfully, do you want to add more clients? Y/N? ";
        cin >> AddMore;

    } while (toupper(AddMore) == 'Y');

}

bool DeleteClientByAccountNumber(string AccountNumber, vector <sClient>& vClients)
{
    sClient Client;
    char Answer = 'n';

    if (FindClientByAccountNumber(AccountNumber, vClients, Client))
    {

        PrintClientCard(Client);

        cout << "\n\nAre you sure you want delete this client? y/n ? ";
        cin >> Answer;
        if (Answer == 'y' || Answer == 'Y')
        {
            MarkClientForDeleteByAccountNumber(AccountNumber, vClients);
            SaveCleintsDataToFile(ClientsFileName, vClients);

            //Refresh Clients 
            vClients = LoadCleintsDataFromFile(ClientsFileName);

            cout << "\n\nClient Deleted Successfully.";
            return true;
        }

    }
    else
    {
        cout << "\nClient with Account Number (" << AccountNumber << ") is Not Found!";
        return false;
    }
}

bool UpdateClientByAccountNumber(string AccountNumber, vector <sClient>& vClients)
{

    sClient Client;
    char Answer = 'n';

    if (FindClientByAccountNumber(AccountNumber, vClients, Client))
    {

        PrintClientCard(Client);
        cout << "\n\nAre you sure you want update this client? y/n ? ";
        cin >> Answer;
        if (Answer == 'y' || Answer == 'Y')
        {
            for (sClient& C : vClients)
            {
                if (C.AccountNumber == AccountNumber)
                {
                    C = ChangeClientRecord(AccountNumber);
                    break;
                }
            }

            SaveCleintsDataToFile(ClientsFileName, vClients);

            cout << "\n\nClient Updated Successfully.";
            return true;
        }

    }
    else
    {
        cout << "\nClient with Account Number (" << AccountNumber << ") is Not Found!";
        return false;
    }
}

string ReadClientAccountNumber()
{
    string AccountNumber = "";

    cout << "\nPlease enter AccountNumber? ";
    cin >> AccountNumber;

    return AccountNumber;

}

void ShowDeleteClientScreen()
{
    cout << "\n-----------------------------------\n";
    cout << "\tDelete Client Screen";
    cout << "\n-----------------------------------\n";

    vector <sClient> vClients = LoadCleintsDataFromFile(ClientsFileName);

    string AccountNumber = ReadClientAccountNumber();

    DeleteClientByAccountNumber(AccountNumber, vClients);
}

void ShowUpdateClientScreen()
{
    cout << "\n-----------------------------------\n";
    cout << "\tUpdate Client Info Screen";
    cout << "\n-----------------------------------\n";

    vector <sClient> vClients = LoadCleintsDataFromFile(ClientsFileName);

    string AccountNumber = ReadClientAccountNumber();

    UpdateClientByAccountNumber(AccountNumber, vClients);

}

void ShowAddNewClientsScreen()
{
    cout << "\n-----------------------------------\n";
    cout << "\tAdd New Clients Screen";
    cout << "\n-----------------------------------\n";

    AddNewClients();
}

void ShowFindClientScreen()
{
    cout << "\n-----------------------------------\n";
    cout << "\tFind Client Screen";
    cout << "\n-----------------------------------\n";

    vector <sClient> vClients = LoadCleintsDataFromFile(ClientsFileName);

    sClient Client;

    string AccountNumber = ReadClientAccountNumber();

    if (FindClientByAccountNumber(AccountNumber, vClients, Client))

        PrintClientCard(Client);

    else

        cout << "\nClient with Account Number[" << AccountNumber << "] is not found!";
}

void ShowEndScreen()
{
    cout << "\n-----------------------------------\n";
    cout << "\tProgram Ends :-)";
    cout << "\n-----------------------------------\n";
}

enum enMainMenueOptions
{
    eListClients = 1, eAddNewClient = 2,
    eDeleteClient = 3, eUpdateClient = 4,
    eFindClient = 5, eExit = 6
};

void GoBackToMainMenue()
{
    cout << "\n\nPress any key to go back to Main Menue...";
    system("pause>0");
    ShowMainMenue();

}

short ReadMainMenueOption()
{
    cout << "Choose what do you want to do? [1 to 6]? ";
    short Choice = 0;
    cin >> Choice;

    return Choice;
}

void PerfromMainMenueOption(enMainMenueOptions MainMenueOption)
{
    switch (MainMenueOption)
    {
    case enMainMenueOptions::eListClients:
    {
        system("cls");
        ShowAllClientsScreen();
        GoBackToMainMenue();
        break;
    }
    case enMainMenueOptions::eAddNewClient:

        system("cls");
        ShowAddNewClientsScreen();
        GoBackToMainMenue();
        break;

    case enMainMenueOptions::eDeleteClient:

        system("cls");
        ShowDeleteClientScreen();
        GoBackToMainMenue();
        break;

    case enMainMenueOptions::eUpdateClient:

        system("cls");
        ShowUpdateClientScreen();
        GoBackToMainMenue();
        break;

    case enMainMenueOptions::eFindClient:

        system("cls");
        ShowFindClientScreen();
        GoBackToMainMenue();
        break;

    case enMainMenueOptions::eExit:
        system("cls");
        ShowEndScreen();
        break;
    }
}

void ShowMainMenue()
{
    system("cls");
    cout << "===========================================\n";
    cout << "\t\tMain Menue Screen\n";
    cout << "===========================================\n";
    cout << "\t[1] Show Client List.\n";
    cout << "\t[2] Add New Client.\n";
    cout << "\t[3] Delete Client.\n";
    cout << "\t[4] Update Client Info.\n";
    cout << "\t[5] Find Client.\n";
    cout << "\t[6] Exit.\n";
    cout << "===========================================\n";
    PerfromMainMenueOption((enMainMenueOptions)ReadMainMenueOption());
}

int main()

{
    ShowMainMenue();
    system("pause>0");
    return 0;
}

```


# Bank Management System - Solution Breakdown

## Overview

This is a comprehensive C++ console application for managing bank clients with file-based data persistence. The system supports creating, reading, updating, and deleting client records (CRUD operations).

## Core Components

### 1. Data Structure

```cpp
struct sClient
{
    string AccountNumber;
    string PinCode;
    string Name;
    string Phone;
    double AccountBalance;
    bool MarkForDelete = false;  // For soft deletion
};
```

**Purpose**: Represents a client with all necessary banking information. The `MarkForDelete` flag enables soft deletion without immediately removing data from the file.

### 2. File Management System

#### File Format

- **File**: `Clients.txt`
- **Format**: Delimited text file using `#` as separator
- **Example**: `ACC001#1234#John Doe#555-0123#5000.50`

#### Key File Operations:

**a) String Splitting (`SplitString`)**

```cpp
vector<string> SplitString(string S1, string Delim)
```

- Breaks delimited strings into individual components
- Uses `find()` and `substr()` to extract data
- Returns vector of string components

**b) Data Conversion Functions**

- `ConvertLinetoRecord()`: Converts file line to client struct
- `ConvertRecordToLine()`: Converts client struct to file line format
- These enable seamless data transformation between file storage and program objects

### 3. Core Functionality Breakdown

#### A) Show Client List

**Function**: `ShowAllClientsScreen()` **Process**:

1. Load all clients from file using `LoadCleintsDataFromFile()`
2. Display formatted table with headers
3. Use `setw()` for proper column alignment
4. Handle empty database scenario

**Key Features**:

- Professional table formatting
- Dynamic client count display
- Empty state handling

#### B) Add New Client

**Function**: `AddNewClients()` → `AddNewClient()` → `ReadNewClient()` **Process**:

1. **Validation**: Check if account number already exists using `ClientExistsByAccountNumber()`
2. **Input Collection**: Gather all client information with input validation
3. **File Append**: Add new record to file using `AddDataLineToFile()`
4. **Loop Option**: Allow adding multiple clients in one session

**Key Features**:

- Duplicate account number prevention
- Input sanitization with `getline(cin >> ws, variable)`
- Append-only file operations to preserve existing data

#### C) Delete Client

**Function**: `ShowDeleteClientScreen()` → `DeleteClientByAccountNumber()` **Process**:

1. **Search**: Find client by account number
2. **Display**: Show client details for confirmation
3. **Confirmation**: Require user confirmation before deletion
4. **Soft Delete**: Mark record for deletion using `MarkClientForDeleteByAccountNumber()`
5. **File Update**: Rewrite file excluding marked records using `SaveCleintsDataToFile()`

**Key Features**:

- Soft deletion approach (safer than immediate deletion)
- User confirmation requirement
- Complete file rewrite to remove deleted records

#### D) Update Client Info

**Function**: `ShowUpdateClientScreen()` → `UpdateClientByAccountNumber()` **Process**:

1. **Locate**: Find client by account number
2. **Display Current**: Show existing client information
3. **Confirmation**: Ask user to confirm update
4. **Input New Data**: Collect updated information using `ChangeClientRecord()`
5. **In-Memory Update**: Modify client data in vector
6. **File Save**: Write entire updated dataset to file

**Key Features**:

- Preserves account number (primary key)
- Shows current data before update
- Requires explicit confirmation

#### E) Find Client

**Function**: `ShowFindClientScreen()` → `FindClientByAccountNumber()` **Process**:

1. **Input**: Get account number to search
2. **Search**: Linear search through client vector
3. **Display**: Show client card if found, error message if not

**Key Features**:

- Simple search by primary key
- Formatted display of client information
- Clear not-found messaging

### 4. Menu System

#### Enum-Based Navigation

```cpp
enum enMainMenueOptions
{
    eListClients = 1, eAddNewClient = 2,
    eDeleteClient = 3, eUpdateClient = 4,
    eFindClient = 5, eExit = 6
};
```

#### Menu Flow

1. **Display Menu**: `ShowMainMenue()` shows options
2. **Get Choice**: `ReadMainMenueOption()` captures user selection
3. **Execute**: `PerfromMainMenueOption()` runs appropriate function
4. **Return**: `GoBackToMainMenue()` provides navigation back

## Technical Design Patterns

### 1. Separation of Concerns

- **UI Functions**: Handle display and user interaction
- **Data Functions**: Manage file I/O and data transformation
- **Business Logic**: Handle validation and core operations

### 2. Error Handling

- File existence checking before operations
- Input validation for account numbers
- Confirmation prompts for destructive operations

### 3. Data Persistence Strategy

- **Read Operations**: Load entire file into memory vector
- **Write Operations**: Overwrite entire file with updated data
- **Add Operations**: Append to existing file

## Key Programming Concepts Demonstrated

### File I/O Operations

- Reading files line by line
- Writing to files with different modes (append/overwrite)
- File stream management

### String Processing

- String splitting and parsing
- Data type conversion (string to double)
- Formatted output using `setw()` and `left`

### Vector Operations

- Dynamic arrays for storing client data
- Reference parameters for modifying vectors
- Range-based for loops for iteration

### User Input Handling

- Mixed input types (strings and numbers)
- Input stream clearing with `cin >> ws`
- Input validation loops

## Strengths of This Solution

1. **Complete CRUD Functionality**: All required operations implemented
2. **Data Persistence**: File-based storage survives program restarts
3. **User-Friendly Interface**: Clear menus and formatted output
4. **Input Validation**: Prevents duplicate accounts and handles errors
5. **Confirmation System**: Prevents accidental data loss
6. **Modular Design**: Functions have single responsibilities

## Areas for Improvement

1. **Performance**: Loading entire file for each operation could be slow with large datasets
2. **Concurrency**: No handling of multiple users accessing the file simultaneously
3. **Security**: PIN codes stored in plain text
4. **Search**: Only supports exact account number matches
5. **Backup**: No automatic backup system for data protection

This solution demonstrates solid understanding of C++ fundamentals, file handling, data structures, and user interface design for console applications.