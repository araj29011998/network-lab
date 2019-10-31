#Server Side

#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>         
#include <sys/socket.h>
#include <netinet/in.h>
#include <errno.h>
#include <string.h>
#include <arpa/inet.h>
#include <unistd.h>
#include<bits/stdc++.h>
#include<string>
using namespace std;

int main(){

int ssock,csock;      
 // creating server and clinet socket discriptor
int a,b,c;
char password[13], word[20], meaning[20], ser[10], rep[3], real[20];
unsigned int len;
//string word2, meaning2, req;

struct sockaddr_in server,client;   
// creating server & client socket object

if((ssock=socket(AF_INET,SOCK_STREAM,0))==-1){     
// creating socket
	perror("socket: ");
	exit(-1);
}

server.sin_family=AF_INET;
server.sin_port=htons(10000);       

// initializing server socket parameters..
server.sin_addr.s_addr=INADDR_ANY;

//inet_addr("127.0.0.1");
bzero(&server.sin_zero,0); 

//appending 8 byte zeroes to 'struct sockaddr_in', to make it equal in size with 'struct sockaddr'.. 


len=sizeof(struct sockaddr_in);

if((bind(ssock,(struct sockaddr *)&server,len))==-1){ 
 // binding port & IP  
	perror("bind error: ");
	exit(-1);
}

if((listen(ssock,5))==-1){     
// listening for client
	perror("listen error: ");                     
	exit(-1);
}


if((csock=accept(ssock,(struct sockaddr *)&client,&len))==-1){  
// accepting connectn
	perror("accept error: ");                         
	exit(-1);
}
while(1){
	while(1){
	/*map<string, string> m;
	ifstream infile;
	infile.open("words.txt");
	string line;
	char a[20], b[20];
	//string a, b;
	while(infile.is_open()){
		while(getline(infile, line)){
			sscanf(line.c_str(), "%s %s", a, b);
			m[a] = b;
		}
		infile.close();
	}
	cout << "map created" << endl;*/
	recv(csock, &word, sizeof(word), 0); 
	cout << "Word Received" << endl;
	int f = 0;
	ifstream infile;
	infile.open("words.txt");
	string line;
	char a[20], b[20];
	//string a, b;
	while(infile.is_open()){
		while(getline(infile, line)){
			sscanf(line.c_str(), "%s %s", a, b);
			if(strcmp(a, word) == 0){
				f = 1;
				strcpy(meaning, b);
				break;
			}
		}
		infile.close();
	}
	if(f == 1){
		cout << "sending meaning " << endl;
		send(csock, &meaning, sizeof(meaning), 0);
	}
	else{
		send(csock, &"no", sizeof("no"), 0);
		recv(csock, &real, sizeof(real), 0);
		ofstream outfile;
		outfile.open("words.txt", std::ios_base::app);
		string pp(real), kk(word);
		outfile << kk << " " << pp << endl;
	}
	
	//string word1(word);
	/*if(m.find(word) != m.end()){
		//meaning2 = m[word1];
		meaning = m[word];
		send(csoc, &meaning, sizeof(meaning), 0);
	}*/
	/*
	recv(csock, &greetings, sizeof(greetings), 0);
	send(csock, &"hello", sizeof("hello"), 0);
	recv(csock, &word, sizeof(word), 0);
	recv(csock, &b, sizeof(b), 0);
	int arr[26];
	int i=0, j = 0;
	while(i < 26) {arr[i] = 0; i+=1;}
	while(j < strlen(word)){
		arr[word[j]-'a']+=1;
		j+=1;
	}
	int id, mx = 0, flag = 0;
	j = 0;
	while(j < 26){
		if(arr[j] == b){
			flag = 1; break;
		}
		if(mx < arr[j]){
			mx = arr[j];
			id = j;
		}
		j+=1;*/
	}
	/*if(flag)
		send(csock, &j, sizeof(j), 0);
	else send(csock, &id, sizeof(id), 0);*/
	

}
close(ssock);
}



#client side


#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>         
#include <sys/socket.h>
#include <netinet/in.h>
#include <errno.h>
#include <string.h>
#include <arpa/inet.h>
#include <unistd.h>
#include<bits/stdc++.h>
using namespace std;
int main(){
int sock;      // client socket discriptor
int a,b,c,i;
unsigned int len;
char ch[3]="no", password[13], word[20], greetings[10], ser[10], rep[3], real[20];
char ch1[3];
char meaning[20];

struct sockaddr_in client;
if((sock=socket(AF_INET,SOCK_STREAM,0))==-1){  // client socket is created..
perror("socket: ");
exit(-1);
}

client.sin_family=AF_INET;
client.sin_port=htons(10000);        // initializing  socket  parameters 
client.sin_addr.s_addr=INADDR_ANY;
//inet_addr("127.0.0.1");
bzero(&client.sin_zero,0); //appending 8 byte zeroes to 'struct sockaddr_in', to make it equal in size with 'struct sockaddr'.. 


len=sizeof(struct sockaddr_in);
if((connect(sock,(struct sockaddr *)&client,len))==-1){  //conneting to client
perror("connect: ");
exit(-1);
}
while(1){
	cout << "Enter the word : ";
	scanf("%s", word);
	send(sock, word, sizeof(word), 0);
	recv(sock, meaning, sizeof(meaning), 0);
	cout << meaning << endl;
	if(strcmp(meaning, "no") == 0){
		cout << "Enter the word meaning : " << endl;
		scanf("%s", real);
		send(sock, real, sizeof(real), 0);
	}
	/*while(1){
		printf("Enter the password : ");
		scanf("%s", password);
		send(sock, &password, sizeof(password), 0);
		recv(sock, &a, sizeof(a), 0);
		if(a == 1) break;
		else continue;
	}
	printf("Password authenticated\n");
	scanf("%s", greetings);
	send(sock, &greetings, sizeof(greetings), 0);
	recv(sock, &ser, sizeof(ser), 0);
	printf("%s\n", ser);
	printf("Enter a word and a number :");
	scanf("%s", word);
	scanf("%d", &b);
	send(sock, &word, sizeof(word), 0);
	send(sock, &b, sizeof(b), 0);
	//printf("send to server word");
	recv(sock, &c, sizeof(c), 0);
	printf("The required character is  : %c", 'a'+c);*/

printf("\nTo exit...press 'no'\n");


scanf("%s",ch1);


if((i=strcmp(ch,ch1))==0){
close(sock);
exit(0);
}
}
}
