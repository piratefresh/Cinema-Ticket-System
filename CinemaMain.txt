#include <iostream>
#include <string>
#include <fstream>
#include <iomanip>
#include <sstream>
#include "CinemaHeader.h"
using namespace std;

int main()
{
	int seatsOpen[FILMS];
	char seats[ROWS][SEATS],
		seats2[ROWS][SEATS],
		seats3[ROWS][SEATS],
		seats4[ROWS][SEATS];
	double avgSales[SALES],
		totalSales[SALES],
		seatsSales[SALES];
	string title[FILMS],
		schedule[FILMS],
		pgRate[FILMS],
		movLength[FILMS],
		genre[FILMS],
		buyer[TOTALSEATS];
	int age = 0, adults = 0, seniors = 0, childs = 0, amountTickets, movieHall, choice;
	double ticketPrice;
	char ch;
	string filename, username, password, customerName, movieTitle;
	ofstream outputFile, outputSeat1File, outputSeat2File, outputSeat3File, outputSeat4File,
		     outputCinemaFile, outputRapportFile, outputMovieStats, outputMovieInfo;
	ifstream inputFile, inputMovieFile, inputSeat1, inputSeat2, inputSeat3, inputSeat4, inputSalesFile, inputTicketBuyers;
	printTitle();

	cout << "Opening the required files\n";
	inputMovieFile.open("movies.txt");
	inputSalesFile.open("moviestats.txt");
	inputSeat1.open("movieseat.txt");
	inputSeat2.open("movieseat2.txt");
	inputSeat3.open("movieseat3.txt");
	inputSeat4.open("movieseat4.txt");
	inputTicketBuyers.open("ticketID.txt");

	if (!inputSeat1)
		cout << "Sorry couldn't not open the file to modify schedule\n";
	if (!inputSeat2)
		cout << "Sorry couldn't not open the file to modify schedule\n";
	if (!inputSeat3)
		cout << "Sorry couldn't not open the file to modify schedule\n";
	if (!inputSeat4)
		cout << "Sorry couldn't not open the file to modify schedule\n";

	cout << "LOADING MOVIE INFORMATIONS\n";

	loadArrayMovieInfo(title, schedule, pgRate, movLength, genre, FILMS, inputMovieFile);

	cout << "LOADING SALES\n";

	loadArrayStats(totalSales, avgSales, seatsOpen, SALES, inputSalesFile); // Initilazies the 

	cout << "LOADING SEATS\n\n\n";

	readCharArrayFile2d(seats, ROWS, inputSeat1);  // initialize a 2d array with file, that carries the reserved seat from previous program executes for hall 1
	readCharArrayFile2d(seats2, ROWS, inputSeat2); // initialize a 2d array with file, that carries the reserved seat from previous program executes for hall 2
	readCharArrayFile2d(seats3, ROWS, inputSeat3); // initialize a 2d array with file, that carries the reserved seat from previous program executes for hall 3
	readCharArrayFile2d(seats4, ROWS, inputSeat4); // initialize a 2d array with file, that carries the reserved seat from previous program executes for hall 4

	system("pause");
	system("CLS");

	while (true) {

		seatsOpen[0] = getOpenSeats(seats); // counting available seats for hall 1
		seatsOpen[1] = getOpenSeats(seats2); // counting available seats for hall 2
		seatsOpen[2] = getOpenSeats(seats3); // counting available seats for hall 3
		seatsOpen[3] = getOpenSeats(seats4); // counting available seats for hall 4

		printUserMenu();
		cout << "\nPlease enter your choice: ";
		choice = getInIntRange(1, 5);

		switch (choice) {
		case MOVIE_SCHEDULE: // MOVIE INFO/SCHEDULE
			printInfoMovies(title, schedule, pgRate, movLength, genre, totalSales, avgSales, seatsOpen);

			break;
		case BUY_TICKET:// BUY TICKETS
			printMovieMenu(title);

			choice = getInIntRange(1, 4);
			switch (choice) {
			case MOVIE1:
				movieTitle = title[0];

				cout << "Buying tickets for " << title[0] << endl;

				cout << "How many tickets do you want to buy: ";
				amountTickets = getIntBiggerThan(1);

				cout << "\nFirst name of the tickets owner: ";
				getline(cin, customerName);

				buyTicket(age, seniors, adults, childs, amountTickets, customerName, pgRate, movieTitle, seats);

				cout << endl;

				ticketPrice = getPrice(seniors, adults, childs);

				cout << "\n\nThanks for your order, the total price is " << ticketPrice << endl;

				totalSales[0] += ticketPrice;

				seatsOpen[0] = getOpenSeats(seats);

				adults = 0, seniors = 0, childs = 0;

				break;
			case MOVIE2:
				movieTitle = title[1];

				outputFile.open("ticketId.txt", ios::app);

				cout << "Buying tickets for " << title[1] << endl;

				cout << "\nFirst name of the tickets owner: ";
				getline(cin, customerName);

				cout << "\nHow many tickets do you want to buy: ";
				amountTickets = getIntBiggerThan(1);

				buyTicket(age, seniors, adults, childs, amountTickets, customerName, pgRate, movieTitle, seats2);

				ticketPrice = getPrice(seniors, adults, childs);
				cout << "\n\nThanks for your order, the total price is " << ticketPrice << endl;

				totalSales[1] += ticketPrice;

				seatsOpen[1] = getOpenSeats(seats2);
				outputFile.close();
				adults = 0, seniors = 0, childs = 0;
				break;
			case MOVIE3:
				movieTitle = title[2];

				cout << "Buying tickets for " << title[2] << endl;

				cout << "\nFirst name of the tickets owner: ";
				getline(cin, customerName);

				cout << "How many tickets do you want to buy: ";
				amountTickets = getIntBiggerThan(1);

				buyTicket(age, seniors, adults, childs, amountTickets, customerName, pgRate, movieTitle, seats3);

				ticketPrice = getPrice(seniors, adults, childs);
				cout << "\n\nThanks for your order, the total price is " << ticketPrice << endl;

				totalSales[2] += ticketPrice;

				seatsOpen[2] = getOpenSeats(seats);
				adults = 0, seniors = 0, childs = 0;
				break;
			case MOVIE4:
				movieTitle = title[3];

				cout << "Buying tickets for " << title[3] << endl;

				cout << "\nFirst name of the tickets owner: ";
				getline(cin, customerName);

				cout << "How many tickets do you want to buy: ";
				amountTickets = getIntBiggerThan(1);

				buyTicket(age, seniors, adults, childs, amountTickets, customerName, pgRate, movieTitle, seats4);

				ticketPrice = getPrice(seniors, adults, childs);
				cout << "\n\nThanks for your order, the total price is " << ticketPrice << endl;

				totalSales[3] += ticketPrice;

				seatsOpen[3] = getOpenSeats(seats);
				adults = 0, seniors = 0, childs = 0;
				break;
			}
			break;
		case ADMIN_MENU: // ADMIN LOGING
			username = loginUser();
			password = loginPass();

			if (isLogged(username, password)) {
				printAdminMenu();
				choice = getInIntRange(1, 6);

				switch (choice) {
				case 1: // EDIT/UPDATE MOVIE INFO
					printMovieMenu(title);

					inputFile.open("movies.txt");

					while (!inputFile) {
						cout << "No Movie Screening\n";
					}

					cout << "\nWhich movies do you want to update? ";
					choice = getInIntRange(1, 4);
					choice -= 1;

					cout << "\nYou have selected " << title[choice] << endl;

					cout << "Please enter the title: ";
					getline(cin, title[choice]);

					cout << "Please enter show time (currently set to " << schedule[choice] << " ): ";
					schedule[choice] = scheduleInput();

					cout << "Please enter the movie age limit: ";
					getline(cin, pgRate[choice]);

					cout << "Please enter the length of movie (Xhrs XXmin)";
					movLength[choice] = movieLengthInput();

					cout << "Please enter the genre (genre/genre/genre): ";
					getline(cin, genre[choice]);

					inputFile.close();

					switch (choice) // RESETS EVERYTHING SO THE UPDATED MOVIE DOSEN'T INHERIT OLD INFO
					{
					case MOVIE1:
						outputSeat1File.open("movieseat.txt");

						initilaizeArray2DArray(seats, ROWS);
						loadArray2DArray(seats, ROWS, outputSeat1File);

						outputMovieStats.open("moviestats.txt");
						resetSalesInvd(totalSales, avgSales, seatsOpen, choice, outputMovieStats);
						outputMovieStats.close();

						outputSeat1File.close();
					case MOVIE2:
						outputSeat2File.open("movieseat2.txt");

						initilaizeArray2DArray(seats2, ROWS);
						loadArray2DArray(seats2, ROWS, outputSeat2File);
						outputMovieStats.open("moviestats.txt");
						resetSalesInvd(totalSales, avgSales, seatsOpen, choice, outputMovieStats);

						outputMovieStats.close();

						outputSeat2File.close();
					case MOVIE3:
						outputSeat3File.open("movieseat3.txt");

						initilaizeArray2DArray(seats3, ROWS);
						loadArray2DArray(seats3, ROWS, outputSeat3File);

						outputMovieStats.open("moviestats.txt");
						resetSalesInvd(totalSales, avgSales, seatsOpen, choice, outputMovieStats);
						outputMovieStats.close();

						outputSeat3File.close();
					case MOVIE4:
						outputSeat4File.open("movieseat4.txt");

						initilaizeArray2DArray(seats4, ROWS);
						loadArray2DArray(seats4, ROWS, outputSeat4File);

						outputMovieStats.open("moviestats.txt");
						resetSalesInvd(totalSales, avgSales, seatsOpen, choice, outputMovieStats);
						outputMovieStats.close();

						outputSeat4File.close();
					}
					break;
				case STATS: // PRINT OUT THE SALE STATS
					loadArraytoArray(seatsSales, seats, seats2, seats3, seats4);

					printRapport(seats, seats2, seats3, seats4, totalSales, seatsSales, seatsOpen, title);

					cout << "\n\nDo you want to print the rapport? "; // PROMPTS THE USER IF THEY WANT TO PRINT TOO FILE
					ch = getYesorNo();

					while (ch == 'y' || ch == 'Y') {

						cout << "\nWhat file do you want to output the rapport? " << endl;
						getline(cin, filename);
						outputRapportFile.open(filename);

						printRapport(seats, seats2, seats3, seats4, totalSales, seatsSales, seatsOpen, title, outputRapportFile);

						outputRapportFile.close();
						break;
					}
					break;
				case CHECK_SEATS: // CHECK SEATS
					printMovieMenu(title);

					cout << "\nWhich movie hall do you want to check seating for: ";
					movieHall = getInIntRange(1, 4);

					if (movieHall == 1) {
						cout << "Seat Map for " << title[0];

						cout << "\n\n";

						printSeatMap(seats, ROWS);

						cout << "Here is the reseted map\n";
					}
					if (movieHall == 2) {
						cout << "Seat Map for " << title[1];

						cout << "\n\n";

						printSeatMap(seats2, ROWS);
					}
					if (movieHall == 3) {
						cout << "Seat Map for " << title[2];

						cout << "\n\n";

						printSeatMap(seats3, ROWS);

						cout << "Here is the reseted map\n";
					}

					if (movieHall == 4) {
						cout << "Seat Map for " << title[3];

						cout << "\n\n";

						printSeatMap(seats4, ROWS);
					}
					break;
				case RESET_HALLS: // RESET HALLS
					printMovieMenu(title);
					cout << "5] Reset all halls";

					cout << "\nWhich movie do you want reset seating for? ";
					choice = getInIntRange(1, 5);

					switch (choice) // CHOOSE WHICH MOVIE TO RESET
					{
					case MOVIE1:
						outputSeat1File.open("movieseat.txt");

						initilaizeArray2DArray(seats, ROWS, outputSeat1File);

						outputSeat1File.close();

						printSeatMap(seats, ROWS);
						cout << "\n\n";

						break;
					case MOVIE2:
						outputSeat2File.open("movieseat2.txt");

						initilaizeArray2DArray(seats2, ROWS,outputSeat2File);

						outputSeat2File.close();

						printSeatMap(seats2, ROWS);
						cout << "\n\n";

						break;
					case MOVIE3:
						outputSeat3File.open("movieseat3.txt");

						initilaizeArray2DArray(seats3, ROWS, outputSeat3File);

						outputSeat3File.close();

						printSeatMap(seats3, ROWS);
						cout << "\n\n";

						break;
					case MOVIE4:
						outputSeat4File.open("movieseat4.txt");

						initilaizeArray2DArray(seats4, ROWS, outputSeat4File);

						outputSeat4File.close();

						printSeatMap(seats4, ROWS);
						cout << "\n\n";

						break;
					case ALL_MOVIES:
						outputSeat1File.open("movieseat.txt");

						initilaizeArray2DArray(seats, ROWS, outputSeat1File);

						outputSeat1File.close();

						outputSeat2File.open("movieseat2.txt");

						initilaizeArray2DArray(seats2, ROWS, outputSeat2File);

						outputSeat2File.close();

						outputSeat3File.open("movieseat3.txt");

						initilaizeArray2DArray(seats3, ROWS, outputSeat3File);

						outputSeat3File.close();

						outputSeat4File.open("movieseat4.txt");

						initilaizeArray2DArray(seats4, ROWS, outputSeat4File);

						outputSeat4File.close();
						break;
					}
					break;
				case RESET_SALES:
					resetSales(totalSales, avgSales, seatsOpen, SALES);

					cout << "Reseting Sales File\n";
					break;
				}
				break;
			}
		case EXIT_PROG:
			cout << "Thanks for using the program\n";

			system("pause");
			return 0;
			break;
		}
		outputMovieInfo.open("movies.txt");
		printMovieInfo(title, schedule, pgRate, movLength, genre, FILMS, outputMovieInfo);
		outputMovieInfo.close();

		outputMovieStats.open("moviestats.txt");
		printSalesFile(totalSales, avgSales, seatsOpen, outputMovieStats);
		outputMovieStats.close();

		outputSeat1File.open("movieseat.txt");
		updateCharArray2dFile(seats, ROWS, outputSeat1File);
		outputSeat1File.close();

		outputSeat2File.open("movieseat2.txt");
		updateCharArray2dFile(seats2, ROWS, outputSeat2File);
		outputSeat2File.close();

		outputSeat3File.open("movieseat3.txt");
		updateCharArray2dFile(seats3, ROWS, outputSeat3File);
		outputSeat3File.close();

		outputSeat4File.open("movieseat4.txt");
		updateCharArray2dFile(seats4, ROWS, outputSeat4File);
		outputSeat4File.close();

		system("pause");
		system("CLS");
	}
	inputMovieFile.close(); inputSalesFile.close(); inputSeat1.close(); inputSeat2.close();	inputSeat3.close(); inputSeat4.close(); inputFile.close(); inputTicketBuyers.close();

	return 0;

}

