#include "CinemaHeader.h"
using namespace std;

void printTitle()
{
	cout << "\n\t          CINEMA MAGNUS                 "
		<< "\n\t   The cinema for the people      \n\n\n";

}

void printStartMenu()
{
	cout << "1] Login\n"
		<< "2] Register\n"
		<< "3] Exit program\n";
}

double getNum()
{
	double num1;

	while (!(cin >> num1)) {
		cin.clear();		cin.ignore(80, '\n');
		cout << "\tPlease no inapropriate characters!\n"
			<< "\tTry again: ";
	}
	cin.ignore(80, '\n');

	return num1;
}

int getInt()
{
	double num1 = getNum();

	while (num1 != int(num1)) {
		cout << "\tYour number is not an integer!\n"
			<< "\tTry again: ";
		num1 = getNum();
	}

	return int(num1);
}

int getInIntRange(int start, int end)
{
	int input1 = getInt();

	while (input1 < start || input1 > end) {
		cout << "\tThat is not a valid option\n";
		cout << "\n\tTry again: ";
		input1 = int(getNum());
	}

	return input1;
}

int getIntBiggerThan(int num2)
{
	int num1 = getInt();

	while (num1 < num2) {
		cout << "Please enter an integer bigger than " << num2 << ": ";
		num1 = getInt();
	}

	return num1;
}

string loginUser()
{
	string username;

	cout << "Enter your login information.\n"
		<< "Enter Username: ";
	cin >> username;

	return username;
}

string loginPass()
{
	string password;

	cout << "Enter password: ";
	cin >> password;

	return password;
}

bool isLogged(string username, string password)
{
	string un, pw;

	ifstream read(username + ".txt");
	getline(read, un); //Gets one line and store it in un.
	getline(read, pw); //Gets one line and store it in pw.
	read.close();

	while (!(username == "admin" && password == "123")) {
		cout << "Wrong login details. Try again\n";
		username = loginUser();
		password = loginPass();
	}
	system("CLS");
	return (un == username && pw == password);
}

void printUserMenu()
{
	cout
		<< "1] Check Movie schedule\n"
		<< "2] Buy Ticket\n"
		<< "3] Admin Menu\n"
		<< "4] Exit program\n";
}

void printAdminMenu()
{
	cout << "ADMIN MENU \n"
		<< "1] Update Movies\n"
		<< "2] Stats\n"
		<< "3] Check seats\n"
		<< "4] Reset Hall\n"
		<< "5] Reset Sales\n"
		<< "6] Exit program\n";
}


void printInfoMovies(string title[FILMS], string schedule[FILMS], string pgRate[FILMS], string movLength[FILMS], string genre[FILMS], double totalSales[SALES], double avgSales[SALES], int seatsOpen[SALES]) // Pipe end. 
{
	cout << "\nThe current movie schedule\n";

	for (int k = 0; k < FILMS; k++) {
		cout << title[k] << endl << schedule[k] << endl
			<< pgRate[k] << endl << movLength[k] << endl
			<< genre[k] << endl
			<< "TOTAL SALES: $" << totalSales[k] << endl
			<< "AVERAGE SALES: $" << avgSales[k] << endl
			<< "AVAILABLE SEATS: " << seatsOpen[k] << endl << endl;
	}
}

void initilaizeArray2DArray(char hall[][SEATS], int rows, ostream &os) // INITILAIZES THE WHOLE SEATING ARRAY TO FALSE RENAME TO Initilaize2dArray
{
	int i, j;
	for (i = 0; i < rows; i++)
	{
		for (j = 0; j < SEATS; j++)
		{
				hall[i][j] = 0;
		}
	}
}


void loadArray2DArray(char hall[][SEATS], int rows, ostream &os)
{
	int i, j;
	for (i = 0; i < rows; i++)
	{
		for (j = 0; j < SEATS; j++)
		{
			if(hall[i][j] == 0)
				hall[i][j] = 'O';  // Vacant Seat
			else
				hall[i][j] = 'X';  // Seat is not available
		}
	}
}

void printSeatMap(char hall[][SEATS], int rows, ostream &os) // PRINTS THE SEAT AS A MAP
{
	for (int j = 0; j < SEATS; j++)
	{
		os << "\t" << j + 1;
	}
	for (int x = 0; x < rows; x++)
	{
		os << right << setw(w1) << "\n" << x + 1;
		for (int y = 0; y < SEATS; y++)
		{
			if (hall[x][y] == 0)
				os << "\t" << 'O';
			else
				os << "\t" << 'X';
			os << setw(w2);
		}
	}
}

char bookSeat(char seats[ROWS][SEATS], string customerName, string movieTitle) // RESERVES THE SEAT IN THE ARRAY
{
	ofstream outputTicketFile, userOutputFile;
	string ticketfile;
	int ticketSeat, ticketRow;

	cout << "\n\nPlease enter row number: ";
	ticketRow = getInIntRange(1, 10);
	ticketRow -= 1;
	cout << "Please enter seat number: ";
	ticketSeat = getInIntRange(1, 5);
	ticketSeat -= 1;

	while (seats[ticketRow][ticketSeat] == true) {
		cout << "Seat is already taken. Please try another seat. \n";

		cout << "\nPlease enter row number: ";
		ticketRow = getInIntRange(1, 10);
		ticketRow -= 1;
		cout << "Please enter seat number: ";
		ticketSeat = getInIntRange(1, 5);
		ticketSeat -= 1;
	}
	cout << endl;

	outputTicketFile.open("ticketID.txt", ios::app);

	printTicketInfo(ticketRow, ticketSeat, customerName, movieTitle);
	printTicketInfo(ticketRow, ticketSeat, customerName, movieTitle, outputTicketFile);

	outputTicketFile.close();

	cout << "\nWhere do you want to store your ticket? ";
	getline(cin, ticketfile);

	userOutputFile.open(ticketfile, ios::app);

	printTicketInfo(ticketRow, ticketSeat, customerName, movieTitle, userOutputFile);

	userOutputFile.close();

	cout << "\t\n\nYou just purchased seat " << ticketSeat + 1 << " in row " << ticketRow + 1 << endl
		<< "Thank you for your order." << endl << endl;

	return seats[ticketRow][ticketSeat] = 1;
}

void loadArrayMovieInfo(string title[FILMS], string schedule[FILMS], string pgRate[FILMS], string movLength[FILMS], string genre[FILMS], int ls, istream &is)  // LOADS MOVIES.TXT INTO ARRAY
{
	for (int i = 0; i < ls; i++) {
		getline(is, title[i]);
		getline(is, schedule[i]);
		getline(is, pgRate[i]);
		getline(is, movLength[i]);
		getline(is, genre[i]);
	}
}

void printPriceList()
{
	cout << "Price List\n"
		<< "Seniors(over 50): $12.00\n"
		<< "Adults(18-50): $14.00\n"
		<< "Child(6-18): $10.00\n";
}

double getPrice(int senior, int adult, int child)
{
	return (senior * SENIOR_PRICE) + (adult * ADULT_PRICE) + (child * CHILD_PRICE);
}

void readCharArrayFile2d(char hall[][SEATS], int rows, istream &is)
{
	for (int x = 0; x < rows; x++)
	{
		for (int y = 0; y < SEATS; y++)
		{
			is >> hall[x][y];
		}
	}
}

void updateCharArray2dFile(char hall[][SEATS], int rows, ostream &os)
{
	for (int x = 0; x < rows; x++)
	{
		for (int y = 0; y < SEATS; y++)
		{
			os << hall[x][y];
		}
	}
}

int getPriceAge(int age, int &seniors, int &adults, int &childs)
{
	return age > 50 ? seniors++ : age > 18 && age ? adults++ : childs++;
}

void printMovieMenu(string title[FILMS])
{
	cout
		<< "1] " << title[0] << endl
		<< "2] " << title[1] << endl
		<< "3] " << title[2] << endl
		<< "4] " << title[3] << endl;
}

void updateMovies(string title[FILMS], string schedule[FILMS], string pgRate[FILMS], string movLength[FILMS], string genre[FILMS], int choice)
{
	cout << "You have selected" << title[choice] << endl;

	cout << "Please enter the title: ";
	getline(cin, title[choice]);

	cout << "Please enter show time (currently set to " << schedule[choice] << " )";
	schedule[choice] = scheduleInput();

	cout << "Please enter the film rating ";
	pgRate[choice] = getInt();

	cout << "Please enter the length of movie (Xhrs XXmin)";
	movLength[choice] = movieLengthInput();

	cout << "Please enter the genre (genre/genre/genre)";
	getline(cin, genre[choice]);
}

void printMovieInfo(string title[FILMS], string schedule[FILMS], string pgRate[FILMS], string movLength[FILMS], string genre[FILMS], int ls, ostream &os)
{
	for (int i = 0; i < FILMS; i++) {
		os << title[i] << endl << schedule[i] << endl << pgRate[i] << endl << movLength[i] << endl << genre[i] << endl;
	}
}

void printSalesFile(double totalSales[], double avgSales[], int seatsOpen[], ostream &os)
{
	for (int i = 0; i < SALES; i++)
		os << totalSales[i] << endl << avgSales[i] << endl << seatsOpen[i] << endl;
}

void loadArrayStats(double a[], double a2[], int a3[], int ls, istream &is)
{
	for (int i = 0; i < ls; i++)
		is >> a[i] >> a2[i] >> a3[i];
}

int getOpenSeats(char seats[ROWS][SEATS])
{
	int seatCount = 0, i, j;

	for (i = 0; i < ROWS; i++)
	{
		for (j = 0; j < SEATS; j++)
		{
			if (seats[i][j] == 0)
			{
				seatCount++;
			}
		}
	}
	return seatCount;
}

double getAvgSales(double a[], int ls)
{
	double avgScore = 0;

	for (int i = 0; i < ls; i++) {
		avgScore += a[i];
	}
	return (ls == 0 ? ls : avgScore / ls);
}

double getSum(double a[], int ls)
{
	double sum = 0;

	for (int i = 0; i < ls; i++) {
		sum += a[i];
	}
	return sum;
}

double getTicketsSold(char seats[ROWS][SEATS])
{
	int seatCount = 0, i, j;

	for (i = 0; i < ROWS; i++)
	{
		for (j = 0; j < SEATS; j++)
		{
			if (seats[i][j] == true)
			{
				seatCount++;
			}
		}
	}
	return seatCount;
}

double getAvgTickets(double a[SALES])
{
	return (a[0] + a[1] + a[2] + a[3]);
}

void loadArraytoArray(double seatsSales[], char seats[ROWS][SEATS], char seats2[ROWS][SEATS], char seats3[ROWS][SEATS], char seats4[ROWS][SEATS])
{
	seatsSales[0] = getTicketsSold(seats);
	seatsSales[1] = getTicketsSold(seats2);
	seatsSales[2] = getTicketsSold(seats3);
	seatsSales[3] = getTicketsSold(seats4);
}

void printArray(const double a[], int ls, ostream &os)       //   done
{
	for (int i = 0; i < ls; i++)
		os << a[i] << endl;
}

string movieLengthInput()
{
	string movhrs = " Hrs ", movmin = " Mins ";
	string runTime;
	int num1, num2;
	stringstream ss;

	cout << "\nHow many hours is the movie? ";
	num1 = getInt();

	cout << "\nAnd how many mins? ";
	num2 = getInt();

	ss << num1 << movhrs << num2 << movmin;
	runTime = ss.str();

	return runTime;
}

string scheduleInput()
{
	string pm = " AM ", am = " PM ";
	string schedule;
	int num1, num2;
	stringstream ss;

	cout << "\nWhat time is the movie showing? ";
	num1 = getInt();

	cout << "1]PM\n"
		<< "2]AM ";
	num2 = getInIntRange(1, 2);

	if (num2 == 1)
		ss << num1 << pm;
	else
		ss << num1 << am;

	schedule = ss.str();

	return schedule;
}


void printSeatInfo(char seats[][SEATS], double seatsSales[], int seatsOpen[], ostream &os)
{
	os << "Avaliable Seats: " << seatsOpen[0]
		<< "Reserved Seats: " << seatsSales[0];
}

void printSaleInfo(double totalSales[], double seatsSales[], ostream &os)
{
	os << "\n\tTotal average of sales: " << getAvgSales(totalSales, SALES) << endl
		<< "\tTotal Earned: " << getSum(totalSales, SALES) << endl
		<< "\tToal Tickets sold: " << getAvgTickets(seatsSales)
		<< "\n\n\tSales Ranking: \n";
}

char getYesorNo()
{
	char choice;
	cin >> choice;		cin.ignore(80, '\n');

	while (choice != 'y' && choice != 'Y' && choice != 'n' && choice != 'N') {
		cout << "Please type Y or N. Try again: ";
		cin >> choice;		cin.ignore(80, '\n');
	}
	return choice;
}

void printRapport(char seats[][SEATS], char seats2[][SEATS], char seats3[][SEATS], char seats4[][SEATS], double totalSales[], double seatsSales[], int seatsOpen[], string title[], ostream &os)
{
	os << "\n\tTODAYS SALES STATS";

	os << "\n";
	os << "\n\t**************************************************\n";

	os << "\n\tTotal average of sales: $" << getAvgSales(totalSales, SALES) << endl;
	os << "\tTotal Earned: $" << getSum(totalSales, SALES) << endl;
	os << "\tToal Tickets sold: $" << getAvgTickets(seatsSales);
	os << "\n\n\tSales Ranking: \n";

	os << "\n\n";
	os << "\n\t**************************************************\n\n";

	os << "\t" << title[0]
		<< " TOTAL SALES: $" << totalSales[0] << endl;
	os << "\t" << title[1]
		<< " TOTAL SALES: $" << totalSales[1] << endl;
	os << "\t" << title[2]
		<< " TOTAL SALES: $" << totalSales[2] << endl;
	os << "\t" << title[3]
		<< " TOTAL SALES: $" << totalSales[3] << endl;

	os << "\n\n";
	os << "\n\t**************************************************\n\n";


	os << "\n\tSEAT MAP FOR: " << title[0];
	os << "\n\n";
	os << "\n\t**************************************************\n\n";

	printSeatMap(seats, ROWS, os);

	os << "\n\nAvaliable Seats: " << seatsOpen[0]
		<< "\nReserved Seats: " << seatsSales[0];

	os << "\n\n\tSEAT MAP FOR: " << title[1];
	os << "\n\n";
	os << "\n\t**************************************************\n\n";

	printSeatMap(seats2, ROWS, os);

	os << "\n\n\tAvaliable Seats: " << seatsOpen[1]
		<< "\n\tReserved Seats: " << seatsSales[1];

	os << "\n\n\tSEAT MAP FOR: " << title[2];
	os << "\n\n";
	os << "\n\t**************************************************\n\n";

	printSeatMap(seats3, ROWS, os);

	os << "\n\n\tAvaliable Seats: " << seatsOpen[2]
		<< "\n\tReserved Seats: " << seatsSales[2];

	os << "\n\n\tSEAT MAP FOR: " << title[3];
	os << "\n\n";
	os << "\n\t**************************************************\n\n";

	printSeatMap(seats4, ROWS, os);

	os << "\n\n\tAvaliable Seats: " << seatsOpen[3]
		<< "\n\tReserved Seats: " << seatsSales[3];

	os << "\n\n";
	os << "\n\t**************************************************\n\n";
}

void resetSales(double totalSales[], double avgSales[], int seatsOpen[], int ls, ostream &os)
{
	for (int x = 0; x < ls; x++)
	{
		totalSales[x] = 0;
		avgSales[x] = 0;
		seatsOpen[x] = 0;
		os << totalSales[x] << endl << avgSales[x] << endl << seatsOpen[x] << endl;
	}
}

void resetSalesInvd(double totalSales[], double avgSales[], int seatsOpen[], int choice, ostream &os)
{
	totalSales[choice] = 0;
	avgSales[choice] = 0;
	seatsOpen[choice] = 0;
	os << totalSales[choice] << endl << avgSales[choice] << endl << seatsOpen[choice] << endl;
}

void buyTicket(int age, int &seniors, int &adults, int &childs, int amountTicket, string customerName, string pgRate[],string movieTitle, char seats[ROWS][SEATS], ostream &os)
{
	for (int i = 0; i < amountTicket; i++) {

		cout << "What is your age? ";
		age = getInt();
		getPriceAge(age, seniors, adults, childs);

		cout << "* avaliable seats\n"
			<< "# reserved seats\n";
		printSeatMap(seats, ROWS);

		seats[ROWS][SEATS] = bookSeat(seats, customerName, movieTitle);
	}
}

void printTicketInfo(int row, int seat, string customerName, string title, ostream &os)
{
	srand(time(0));

	int id;

	id = rand() % 100;
	if(id == 0)
		id = rand() % 100;

	os << "Your Ticket | Ticket Holder: " <<  customerName << " MOVIE: " << title << " ROW: "<< row + 1 << " SEAT: " << seat + 1 << " TICKET ID: " << id << endl;
}

