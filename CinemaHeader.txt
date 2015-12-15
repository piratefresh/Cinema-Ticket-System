#include <iostream>
#include <string>
#include <fstream>
#include <iomanip>
#include <sstream>
#include <vector>
using namespace std;

//Global variables
static const int SEATS = 5;      // Number of columns in each array
static const int ROWS = 10; // Number of rows in table1
static const int FILMS = 4;
const int TOTALSEATS = 200;
const double SENIOR_PRICE = 12.00, ADULT_PRICE = 14.00, CHILD_PRICE = 10.00;
const int SALES = 4;
const int MOVIE_SCHEDULE = 1, BUY_TICKET = 2, ADMIN_MENU = 3,
EXIT_PROG = 4, MOVIE1 = 1, MOVIE2 = 2, MOVIE3 = 3, MOVIE4 = 4, ALL_MOVIES = 5,
STATS = 2, CHECK_SEATS = 3, RESET_HALLS = 4, RESET_SALES = 5; // MENU CHOICES

const int w1 = 4, w2 = 2;

void printTitle();
void printStartMenu();
double getNum();
int getInt();
int getInIntRange(int start, int end);
int getIntBiggerThan(int num2);

//Program functions

//LOGIN FUNCTIONS
string loginUser();
string loginPass();
bool isLogged(string username, string password);

// PRE-CONDITIONS:  Os must be connected.
// POST-CONDITIONS: Prints the title to os.
void printUserMenu(); // prints the main menu

// PRE-CONDITIONS:  Os must be connected.
// POST-CONDITIONS: Prints the seating map to os.
void printSeatMap(char array[][SEATS], int ls, ostream &os = cout); // prints out the seating map, formated

// PRE-CONDITIONS:  Os must be connected.
// POST-CONDITIONS: Prints the ticket prices to os.
void printPriceList(); // prints the prices for each age class

// PRE-CONDITIONS:  0 <= "size" <= logical size of a[].  
//				    Os must be connected.
// POST-CONDITIONS: Prints the first "size" elements of a[] to "os".
void printArray(const double a[], int ls, ostream &os = cout); // prints out an array

// PRE-CONDITIONS:  Os must be connected.
// POST-CONDITIONS: Prints the admin menu to os.
void printAdminMenu(); // prints the admin menu

// PRE-CONDITIONS:  Os must be connected.
// POST-CONDITIONS: Prints the first "size" elements of movie info/sales arrays to "os".
void printInfoMovies(string title[FILMS], string schedule[FILMS], string pgRate[FILMS], string movLength[FILMS], string genre[FILMS], double totalSales[SALES], double avgSales[SALES], int seatsOpen[SALES]); // prints out the movie info.

// PRE-CONDITIONS:  Os must be connected.
// POST-CONDITIONS: Prints the movie title menu to os.
void printMovieMenu(string title[FILMS]); // prints the movie menu

// PRE-CONDITIONS:  Os must be connected.
// POST-CONDITIONS: Prints the sales stats rapport.
void printRapport(char seats[][SEATS], char seats2[][SEATS], char seats3[][SEATS], char seats4[][SEATS], double totalSales[], double seatsSales[], int seatsOpen[], string title[], ostream &os = cout);

// PRE-CONDITIONS:  Os must be connected.
// POST-CONDITIONS: Prints only movie information.
void printMovieInfo(string title[FILMS], string schedule[FILMS], string pgRate[FILMS], string movLength[FILMS], string genre[FILMS], int ls, ostream &os = cout); // writes to the movie information file (movies.txt)

// PRE-CONDITIONS:  Os must be connected.
// POST-CONDITIONS: Prints the sales stats.
void printSalesFile(double totalSales[], double avgSales[], int seatsOpen[], ostream &os = cout); // writes to the salesfile(moviestats.txt)

// PRE-CONDITIONS:  Os must be connected.
// POST-CONDITIONS: Prints the customers ticket.
void printTicketInfo(int row, int seat, string customerName, string movieTitle, ostream &os = cout);


// LOADERS

// PRE-CONDITIONS:  Is must be connected.
//					0 < ls
// POST-CONDITIONS: Loads arrays with sales stats for movies.
void loadArrayStats(double a[], double a2[], int a3[], int ls, istream &os = cin); // loads the moviestats.txt

// PRE-CONDITIONS:  Is must be connected.
// POST-CONDITIONS: Initializes the the array with seat information
//					X = reserved, O free.
void loadArray2DArray(char section[][SEATS], int rows, ostream &os = cout); // loads the movieseat files

// PRE-CONDITIONS:  Is must be connected.
// POST-CONDITIONS: Loads 2d arrays into a 1d array
void loadArraytoArray(double seatsSales[], char seats[ROWS][SEATS], char seats2[ROWS][SEATS], char seats3[ROWS][SEATS], char seats4[ROWS][SEATS]); // loads an array to another array. (MAYBE REDUNDANT)

// PRE-CONDITIONS:  Is must be connected.
//					0 < ls
// POST-CONDITIONS: Loads the array with movie information.
void loadArrayMovieInfo(string title[FILMS], string schedule[FILMS], string pgRate[FILMS], string movLength[FILMS], string genre[FILMS], int ls, istream &is = cin); // loads in the movie info to array (RENAME)

// PRE-CONDITIONS:  Is must be connected.
//					0 < ls
// POST-CONDITIONS: Resets one movie sales array.
void resetSales(double totalSales[], double avgSales[], int seatsOpen[], int ls, ostream &os = cout);

// PRE-CONDITIONS:  Is must be connected.
// POST-CONDITIONS: Resets all the movie sales array.
void resetSalesInvd(double totalSales[], double avgSales[], int seatsOpen[], int choice, ostream &os = cout);

// PRE-CONDITIONS:  Is must be connected.
//					0 < ROWS.
// POST-CONDITIONS: Sets the entire hall[][] to 0.
void initilaizeArray2DArray(char hall[][SEATS], int rows, ostream &os = cout);


//GETTERS

// PRE-CONDITIONS:  Is must be connected.
// POST-CONDITIONS: Resets the movie sales array.
double getPrice(int seniors, int adults, int childs); // gets the price for the ticket
int getPriceAge(int age, int &seniors, int &adults, int &childs); // gets the price for the age
double getAvgSales(double a[], int ls); // gets the average of the total sales
double getSum(double a[], int ls); // gets the sum of all tickets sold
double getTicketsSold(char a[ROWS][SEATS]); //gets the tickets sold hall
double getAvgTickets(double a[SALES]); // gets the average from tickets sold from all movies
string movieLengthInput(); // Templates the input
string scheduleInput(); // Templates the input for movie time

//PRE-CONDITIONS:  NONE.
//POST-CONDITIONS: Returns 'y' or 'Y'.
//				   Removes everything else from the input buffer.
char getYesorNo();
void buyTicket(int age, int &seniors, int &adults, int &childs, int amountTicket, string customerName, string pgRate[], string movieTitle, char seats[ROWS][SEATS], ostream &os = cout);

// FILES
void readCharArrayFile2d(char hall[][SEATS], int rows, istream &is = cin); // reads file that contains a 2d char array
void readArrayFile(int sales[], int ls, istream &os = cin); // reads file that contains a char array
void updateCharArray2dFile(char hall[][SEATS], int ls, ostream &os = cout); // Updates a char 2d array file
void updateMovies(string title[FILMS], string schedule[FILMS], string pgRate[FILMS], string movLength[FILMS], string genre[FILMS]); // Admin can edit/update the movie information.

int getOpenSeats(char seats[ROWS][SEATS]); // Counts the available seats.
char bookSeat(char seats[ROWS][SEATS], string customerName, string movieTitle); // Books the seat inside the array.
