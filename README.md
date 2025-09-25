#include <iostream>
#include <string>
#include <set>
#include <map>
#include <sstream>
#include <vector>
#include <algorithm>
#ifdef _WIN32
#include <windows.h>
#endif

using namespace std;

struct AFD {
    set<string> Q;
    set<string> Sigma;
    string q0;
    set<string> F;
    map<pair<string,string>, string> delta;

    bool simulate(const string &input) const {
        string state = q0;
        for (char c : input) {
            string sym(1, c);
            auto it = delta.find({state, sym});
            if (it == delta.end()) return false;
            state = it->second;
        }
        return F.count(state) > 0;
    }

    void draw_ascii() const {
        cout << "\n=== Representação do AFD ===\n";
        cout << "Estados: ";
        for (auto &q : Q) cout << q << " ";
        cout << "\nAlfabeto: ";
        for (auto &s : Sigma) cout << s << " ";
        cout << "\nEstado inicial: " << q0;
        cout << "\nEstados finais: ";
        for (auto &f : F) cout << f << " ";
        cout << "\n\nTransições:\n";
        for (auto &t : delta) {
            cout << "  " << t.first.first << " --" << t.first.second 
                 << "--> " << t.second << "\n";
        }
        cout << "=============================\n\n";
    }
};

int main() {
    #ifdef _WIN32
    SetConsoleOutputCP(CP_UTF8);
    SetConsoleCP(CP_UTF8);
    #endif
    AFD afd;
    string line;

    cout << "Digite os estados (ex: q0 q1 q2):\n> ";
    getline(cin, line);
    {
        stringstream ss(line);
        string x;
        while (ss >> x) afd.Q.insert(x);
    }

    cout << "Digite o alfabeto (ex: 0 1):\n> ";
    getline(cin, line);
    {
        stringstream ss(line);
        string x;
        while (ss >> x) afd.Sigma.insert(x);
    }

    cout << "Digite o estado inicial:\n> ";
    cin >> afd.q0;
    afd.Q.insert(afd.q0);

    cout << "Digite os estados finais (separe por espaço, termine com #):\n> ";
    {
        string f;
        while (cin >> f && f != "#") afd.F.insert(f);
    }

    cout << "Digite as transições (estado simbolo novo_estado). Digite 'fim' para parar:\n";
    while (true) {
        string st, sym, nxt;
        cin >> st;
        if (st == "fim") break;
        cin >> sym >> nxt;
        afd.delta[{st, sym}] = nxt;
        afd.Q.insert(st);
        afd.Q.insert(nxt);
    }

    afd.draw_ascii();

    cout << "Modo de simulação: digite cadeias (ex: 1010). Digite 'sair' para encerrar.\n";
    while (true) {
        cout << "cadeia> ";
        string w;
        cin >> w;
        if (w == "sair") break;
        cout << (afd.simulate(w) ? "ACEITA" : "REJEITADA") << "\n";
    }

    return 0;
}
