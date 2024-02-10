#include <iostream>
#include <iomanip>
#include <vector>
#include <string>
#include <tuple>
#include <stdexcept>

using namespace std;

// Constants for cost values
const double COACH_COST[] = {150, 190, 225};
const double MEAL_COST = 14.00;
const double TICKET_COST[] = {21.00, 13.50, 13.00, 20.00, 19.00};

// Minimum and maximum number of senior citizens
const int MIN_SENIORS = 10;
const int MAX_SENIORS = 36;

// Minimum number of carers and additional carer threshold
const int MIN_CARERS = 2;
const int ADDITIONAL_CARER_THRESHOLD = 24;

// Function to calculate the total cost, cost per person, and if an additional carer is needed
tuple<double, double, bool> calculateCost(int numPeople) {
    try {
        if (MIN_SENIORS <= numPeople && numPeople <= MAX_SENIORS) {
            int index;
            if (12 <= numPeople && numPeople <= 16) {
                index = 0;
            } else if (17 <= numPeople && numPeople <= 26) {
                index = 1;
            } else {
                index = 2;
            }

            double totalCost = COACH_COST[index] + numPeople * (MEAL_COST + TICKET_COST[index]);
            double costPerPerson = totalCost / numPeople;

            bool additionalCarerNeeded = numPeople > ADDITIONAL_CARER_THRESHOLD;

            return make_tuple(totalCost, costPerPerson, additionalCarerNeeded);
        } else {
            throw invalid_argument("Invalid number of people. The number of senior citizens should be between 10 and 36.");
        }
    } catch (const invalid_argument& e) {
        throw invalid_argument("Invalid input. Please enter a valid number of senior citizens.");
    }
}

// Function to record and display the details of people on the outing
void recordAndDisplayDetails(vector<string>& names, vector<double>& payments, int totalPeople) {
    for (int i = 0; i < totalPeople; ++i) {
        string name;
        double payment;

        cout << "Enter the name of person " << (i + 1) << ": ";
        cin.ignore();
        getline(cin, name);

        cout << "Enter the amount paid by " << name << ": $";
        cin >> payment;

        names.push_back(name);
        payments.push_back(payment);
    }
}

// Function to calculate and display the break-even point or profit
void calculateAndDisplayBreakEven(double totalCost, const vector<double>& payments) {
    double totalPayments = 0;

    for (double payment : payments) {
        totalPayments += payment;
    }

    cout << fixed << setprecision(2);
    cout << "\nTotal Cost: $" << totalCost << endl;
    cout << "Total Amount Collected: $" << totalPayments << endl;

    if (totalPayments >= totalCost) {
        double profit = totalPayments - totalCost;
        cout << "Profit: $" << profit << endl;
    } else {
        double loss = totalCost - totalPayments;
        cout << "Loss: $" << loss << endl;
    }
}

int main() {
    try {
        int numPeople;
        cout << "Enter the number of senior citizens: ";
        cin >> numPeople;

        if (cin.fail() || numPeople < MIN_SENIORS || numPeople > MAX_SENIORS) {
            throw invalid_argument("Invalid input. Please enter a valid number of senior citizens between 10 and 36.");
        }

        cin.ignore(); // Clear any remaining newline characters in the input buffer

        auto result = calculateCost(numPeople);

        if (get<2>(result)) {
            cout << "\nTASK 1 - Cost Calculation:\n";
            cout << "Total Cost: $" << get<0>(result) << endl;
            cout << "Cost per Person: $" << get<1>(result) << endl;
            cout << "Additional carer needed." << endl;

            // Task 2
            cout << "\nTASK 2 - Record Details:\n";
            vector<string> names;
            vector<double> payments;

            int totalPeople = numPeople + MIN_CARERS; // Including carers
            recordAndDisplayDetails(names, payments, totalPeople);

            // Task 3
            cout << "\nTASK 3 - Break-even Point or Profit Calculation:\n";
            calculateAndDisplayBreakEven(get<0>(result), payments);
        } else {
            cout << "Sorry, the outing cannot proceed with the given number of senior citizens." << endl;
        }

    } catch (const invalid_argument& e) {
        cout << e.what() << endl;
    }

    return 0;
}

