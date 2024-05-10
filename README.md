#include <iostream>
#include <unordered_set>
#include <unordered_map>
#include <vector>
#include <string>
#include <sstream>

using namespace std;

struct ASTNode {
    string value;
    vector<ASTNode*> children;

    ASTNode(string val) : value(val) {}
};

void printAST(ASTNode* root, int indent = 0) {
    if (!root) return;

    for (int i = 0; i < indent; i++) cout << "  ";
    cout << root->value << endl;

    for (auto child : root->children) {
        printAST(child, indent + 1);
    }
}

int main() {
    unordered_set<string> variables;
    unordered_set<string> terminales;
    unordered_multimap<string, vector<string>> producciones;
    string simboloInicial;

    cout << "Ingrese las variables (separadas por espacios): ";
    string input;
    getline(cin, input);
    istringstream iss(input);
    string var;
    while (iss >> var) {
        variables.insert(var);
    }

    cout << "Ingrese los terminales (separados por espacios): ";
    getline(cin, input);
    iss = istringstream(input);
    while (iss >> var) {
        terminales.insert(var);
    }

    cout << "Ingrese las reglas de producción (en formato A->X1X2...Xn, una por línea, termine con una línea vacía):\n";
    while (getline(cin, input) && !input.empty()) {
        istringstream iss(input);
        string variable, flecha, produccion;
        iss >> variable >> flecha >> produccion;
        vector<string> derivaciones;
        string token;
        istringstream prodStream(produccion);
        while (prodStream >> token) {
            derivaciones.push_back(token);
        }
        producciones.insert(make_pair(variable, derivaciones));
    }

    cout << "Ingrese el símbolo inicial: ";
    cin >> simboloInicial;

    ASTNode* root = new ASTNode(simboloInicial);
    unordered_multimap<string, vector<string>>::iterator it;
    for (it = producciones.begin(); it != producciones.end(); it++) {
        string variable = it->first;
        vector<string> derivaciones = it->second;
        ASTNode* variableNode = new ASTNode(variable);
        for (string derivacion : derivaciones) {
            ASTNode* derivacionNode = new ASTNode(derivacion);
            variableNode->children.push_back(derivacionNode);
        }
        root->children.push_back(variableNode);
    }

    cout << "\nGramática generada:\n";
    printAST(root);

    return 0;
}
