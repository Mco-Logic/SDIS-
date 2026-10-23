SDIS CODE C++

#include <iostream>
#include <string>
#include <vector>
#include <fstream>
#include <ctime>
#include <algorithm>

using namespace std;

// ================= TIME =================

string getCurrentTime()
{
    time_t now = time(0);

    char buf[80];

    strftime(
        buf,
        sizeof(buf),
        "%d-%b-%Y %H:%M",
        localtime(&now)
    );

    return string(buf);
}

// ================= HASH =================

int simpleHash(string data)
{
    int hash = 0;

    for(char c : data)
    {
        hash += c * 31;
    }

    return hash;
}

// ================= STRUCTURES =================

struct Evidence
{
    string description;
    int integrityHash;
};

struct Suspect
{
    string name;
    string reason;
    int score = 0;
};

struct Case
{
    string caseNumber;
    string crimeType;
    string summary;
    string createdBy;

    vector<Evidence> evidenceList;
    vector<Suspect> suspects;

    int caseIntegrity;
};

struct Request
{
    string username;
    string action;
    string time;
};

struct LoginRecord
{
    string username;
    string role;
    string time;
};

struct TamperRecord
{
    string username;
    string caseNumber;
    string action;
    string time;
};

// ================= GLOBAL DATA =================

vector<Case> cases;

vector<Request> pendingRequests;

vector<LoginRecord> loginHistory;

vector<TamperRecord> tamperHistory;

vector<string> approvedCreateUsers;

vector<string> approvedReviewUsers;

string currentUser = "";
string currentRole = "";

// ================= LOGIN HISTORY =================

void recordLogin(string username, string role)
{
    LoginRecord l;

    l.username = username;
    l.role = role;
    l.time = getCurrentTime();

    loginHistory.push_back(l);
}

// ================= ACCESS CHECK =================

bool hasCreateAccess(string username)
{
    return find(
        approvedCreateUsers.begin(),
        approvedCreateUsers.end(),
        username
    ) != approvedCreateUsers.end();
}

bool hasReviewAccess(string username)
{
    return find(
        approvedReviewUsers.begin(),
        approvedReviewUsers.end(),
        username
    ) != approvedReviewUsers.end();
}

// ================= LOGIN =================

bool detectiveLogin()
{
    string username;
    string password;

    cout << "\n===== DETECTIVE LOGIN =====\n";

    cout << "Enter username: ";
    getline(cin, username);

    cout << "Enter password: ";
    getline(cin, password);

    if(password == "Detective#Secure2026")
    {
        currentUser = username;
        currentRole = "DETECTIVE";

        recordLogin(username, "DETECTIVE");

        cout << "\nLogin successful.\n";

        return true;
    }

    cout << "\nInvalid password.\n";

    return false;
}

bool commanderLogin()
{
    string username;
    string password;

    cout << "\n===== COMMANDER LOGIN =====\n";

    cout << "Enter commander name: ";
    getline(cin, username);

    cout << "Enter password: ";
    getline(cin, password);

    if(password == "Commander#Secure2026")
    {
        currentUser = username;
        currentRole = "COMMANDER";

        recordLogin(username, "COMMANDER");

        cout << "\nCommander access granted.\n";

        return true;
    }

    cout << "\nInvalid password.\n";

    return false;
}

// ================= REQUEST ACCESS =================

void requestCreateAccess()
{
    Request r;

    r.username = currentUser;
    r.action = "CREATE CASE";
    r.time = getCurrentTime();

    pendingRequests.push_back(r);

    cout << "\nCREATE CASE request sent.\n";
}

void requestReviewAccess()
{
    Request r;

    r.username = currentUser;
    r.action = "REVIEW CASE";
    r.time = getCurrentTime();

    pendingRequests.push_back(r);

    cout << "\nREVIEW CASE request sent.\n";
}

// ================= CREATE CASE =================

void createCase()
{
    if(!hasCreateAccess(currentUser))
    {
        cout << "\nAccess not granted yet.\n";
        return;
    }

    Case c;

    c.createdBy = currentUser;

    cout << "\n===== CREATE CASE =====\n";

    cout << "Case Number: ";
    getline(cin, c.caseNumber);

    cout << "Crime Type: ";
    getline(cin, c.crimeType);

    cout << "Summary: ";
    getline(cin, c.summary);

    int evidenceCount;

    cout << "\nEvidence Count: ";
    cin >> evidenceCount;
    cin.ignore();

    for(int i = 0; i < evidenceCount; i++)
    {
        Evidence e;

        cout << "Evidence "
             << i + 1
             << ": ";

        getline(cin, e.description);

        e.integrityHash =
            simpleHash(e.description);

        c.evidenceList.push_back(e);
    }

    int suspectCount;

    cout << "\nSuspect Count: ";
    cin >> suspectCount;
    cin.ignore();

    for(int i = 0; i < suspectCount; i++)
    {
        Suspect s;

        cout << "Suspect Name: ";
        getline(cin, s.name);

        cout << "Reason: ";
        getline(cin, s.reason);

        s.score = 10;

        c.suspects.push_back(s);
    }

    c.caseIntegrity =
        simpleHash(
            c.caseNumber +
            c.crimeType +
            c.summary
        );

    cases.push_back(c);

    cout << "\nCASE CREATED SUCCESSFULLY.\n";
}

// ================= REVIEW CASE =================

void reviewCase()
{
    if(!hasReviewAccess(currentUser))
    {
        cout << "\nReview access not granted.\n";
        return;
    }

    vector<int> ownedCases;

    cout << "\n===== YOUR CASES =====\n";

    for(int i = 0; i < cases.size(); i++)
    {
        if(cases[i].createdBy == currentUser)
        {
            ownedCases.push_back(i);

            cout << ownedCases.size()
                 << ". "
                 << cases[i].caseNumber
                 << " | "
                 << cases[i].crimeType
                 << endl;
        }
    }

    if(ownedCases.empty())
    {
        cout << "\nNo cases found.\n";
        return;
    }

    int choice;

    cout << "\nSelect case: ";
    cin >> choice;
    cin.ignore();

    if(choice < 1 || choice > ownedCases.size())
    {
        cout << "Invalid choice.\n";
        return;
    }

    Case &c = cases[ownedCases[choice - 1]];

    cout << "\n===== CASE REPORT =====\n";

    cout << "Case Number: "
         << c.caseNumber
         << endl;

    cout << "Crime Type: "
         << c.crimeType
         << endl;

    cout << "Summary: "
         << c.summary
         << endl;

    int verifyCase =
        simpleHash(
            c.caseNumber +
            c.crimeType +
            c.summary
        );

    if(verifyCase != c.caseIntegrity)
    {
        cout << "\nWARNING: CASE TAMPERING DETECTED!\n";
    }

    cout << "\nEvidence:\n";

    for(auto &e : c.evidenceList)
    {
        cout << "- "
             << e.description
             << endl;

        if(simpleHash(e.description)
           != e.integrityHash)
        {
            cout << "WARNING: EVIDENCE TAMPERED!\n";
        }
    }

    cout << "\nSuspects:\n";

    for(auto &s : c.suspects)
    {
        cout << s.name
             << " | Score: "
             << s.score
             << endl;
    }
}

// ================= MODIFY EVIDENCE =================

void modifyEvidence()
{
    vector<int> ownedCases;

    cout << "\n===== YOUR CASES =====\n";

    for(int i = 0; i < cases.size(); i++)
    {
        if(cases[i].createdBy == currentUser)
        {
            ownedCases.push_back(i);

            cout << ownedCases.size()
                 << ". "
                 << cases[i].caseNumber
                 << " | "
                 << cases[i].crimeType
                 << endl;
        }
    }

    if(ownedCases.empty())
    {
        cout << "\nNo cases found.\n";
        return;
    }

    int caseChoice;

    cout << "\nSelect case: ";
    cin >> caseChoice;
    cin.ignore();

    if(caseChoice < 1
       || caseChoice > ownedCases.size())
    {
        cout << "Invalid choice.\n";
        return;
    }

    Case &c =
        cases[ownedCases[caseChoice - 1]];

    if(c.evidenceList.empty())
    {
        cout << "\nNo evidence found.\n";
        return;
    }

    cout << "\n===== EVIDENCE =====\n";

    for(int i = 0;
        i < c.evidenceList.size();
        i++)
    {
        cout << i + 1
             << ". "
             << c.evidenceList[i].description
             << endl;
    }

    int evidenceChoice;

    cout << "\nSelect evidence: ";
    cin >> evidenceChoice;
    cin.ignore();

    if(evidenceChoice < 1
       || evidenceChoice > c.evidenceList.size())
    {
        cout << "Invalid choice.\n";
        return;
    }

    cout << "Enter new evidence: ";

    getline(
        cin,
        c.evidenceList[evidenceChoice - 1]
        .description
    );

    TamperRecord t;

    t.username = currentUser;
    t.caseNumber = c.caseNumber;
    t.action = "MODIFIED EVIDENCE";
    t.time = getCurrentTime();

    tamperHistory.push_back(t);

    cout << "\nEvidence modified.\n";
}

// ================= COMMANDER FEATURES =================

void viewPendingRequests()
{
    if(pendingRequests.empty())
    {
        cout << "\nNo pending requests.\n";
        return;
    }

    cout << "\n===== PENDING REQUESTS =====\n";

    for(int i = 0;
        i < pendingRequests.size();
        i++)
    {
        cout << i + 1
             << ". "
             << pendingRequests[i].username
             << " requested "
             << pendingRequests[i].action
             << " at "
             << pendingRequests[i].time
             << endl;
    }

    int choice;

    cout << "\nSelect request: ";
    cin >> choice;
    cin.ignore();

    if(choice < 1
       || choice > pendingRequests.size())
    {
        cout << "Invalid request.\n";
        return;
    }

    Request selected =
        pendingRequests[choice - 1];

    char decision;

    cout << "\nApprove request? (Y/N): ";
    cin >> decision;
    cin.ignore();

    if(decision == 'Y'
       || decision == 'y')
    {
        if(selected.action == "CREATE CASE")
        {
            approvedCreateUsers.push_back(
                selected.username
            );
        }

        else if(selected.action
                == "REVIEW CASE")
        {
            approvedReviewUsers.push_back(
                selected.username
            );
        }

        cout << "\nAccess granted.\n";
    }

    else
    {
        cout << "\nAccess denied.\n";
    }

    pendingRequests.erase(
        pendingRequests.begin()
        + (choice - 1)
    );
}

// ================= VIEW LOGIN HISTORY =================

void viewLoginHistory()
{
    cout << "\n===== LOGIN HISTORY =====\n";

    for(auto &l : loginHistory)
    {
        cout << l.username
             << " (" << l.role << ") "
             << "logged in at "
             << l.time
             << endl;
    }
}

// ================= VIEW TAMPER HISTORY =================

void viewTamperHistory()
{
    cout << "\n===== TAMPER HISTORY =====\n";

    if(tamperHistory.empty())
    {
        cout << "No tampering detected.\n";
        return;
    }

    for(auto &t : tamperHistory)
    {
        cout << t.username
             << " "
             << t.action
             << " in case "
             << t.caseNumber
             << " at "
             << t.time
             << endl;
    }
}

// ================= DETECTIVE MENU =================

void detectiveMenu()
{
    int choice;

    do
    {
        cout << "\n===== DETECTIVE PANEL =====\n";

        cout << "1. Request Create Access\n";
        cout << "2. Create Case\n";
        cout << "3. Request Review Access\n";
        cout << "4. Review Case\n";
        cout << "5. Modify Evidence\n";
        cout << "6. Logout\n";

        cout << "Choice: ";

        cin >> choice;
        cin.ignore();

        switch(choice)
        {
            case 1:
                requestCreateAccess();
                break;

            case 2:
                createCase();
                break;

            case 3:
                requestReviewAccess();
                break;

            case 4:
                reviewCase();
                break;

            case 5:
                modifyEvidence();
                break;
        }

    }
    while(choice != 6);
}

// ================= COMMANDER MENU =================

void commanderMenu()
{
    int choice;

    do
    {
        cout << "\n===== COMMANDER PANEL =====\n";

        cout << "1. View Pending Requests\n";
        cout << "2. View Login History\n";
        cout << "3. View Tamper History\n";
        cout << "4. Logout\n";

        cout << "Choice: ";

        cin >> choice;
        cin.ignore();

        switch(choice)
        {
            case 1:
                viewPendingRequests();
                break;

            case 2:
                viewLoginHistory();
                break;

            case 3:
                viewTamperHistory();
                break;
        }

    }
    while(choice != 4);
}

// ================= MAIN =================

int main()
{
    int choice;

    do
    {
        cout << "\n===== SECURE DIGITAL INVESTIGATION SYSTEM =====\n";

        cout << "1. Login as Detective\n";
        cout << "2. Login as Commander\n";
        cout << "3. Exit\n";

        cout << "Choice: ";

        cin >> choice;
        cin.ignore();

        switch(choice)
        {
            case 1:

                if(detectiveLogin())
                {
                    detectiveMenu();
                }

                break;

            case 2:

                if(commanderLogin())
                {
                    commanderMenu();
                }

                break;
        }

    }
    while(choice != 3);

    cout << "\nSystem closed.\n";

    return 0;
}
