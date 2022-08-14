# Telephone-Management-System

#include<fstream.h>
#include<conio.h>
#include<stdio.h>
#include<iomanip.h>
#include<ctype.h>
#include<string.h>


////////////////////////////////////////////////////////////////////////
			 /*DEFINING CLASS*/
////////////////////////////////////////////////////////////////////////

class customer
{ long int cNo;
  char name[15],address[30],email[20];
  char rmn[11];
  int lReading,cReading;
  public:

     customer()                                     //CONSTRUCTOR
     { ofstream f;
       f.open("CUSTOMER.DAT",ios::binary|ios::ate); //Creation of file//
       f.close();                                   //CUSTOMER.DAT//
     }
     void readData()
     { cout<<"\nCustomer no :"; cin>>cNo;
       cout<<"\nName:"; gets(name);
       cout<<"\nAddress:";  gets(address);
       cout<<"\nRegistered mobile no:"; gets(rmn);
       cout<<"\nEmail ID :"; gets(email);
       cout<<"\nLast reading :"; cin>>lReading;
       cout<<"\nCurrent reading :"; cin>>cReading;
     }
     void showData()
     { cout<<cNo<<setw(10)<<name<<setw(15)<<address<<setw(25)
       <<rmn<<setw(11)<<lReading<<setw(15)<<cReading;
     }
     long rCNo(){ return cNo;}                 //
     int rLReading(){ return lReading;}        //
     int rCReading(){ return cReading;}        //  RETURN PRIVATE
     char* rName(){ return name;}              //  VALUES FUNCTIONS
     char* rAddress(){ return address;}        //
     char* rEmail(){ return email;}            //
     char* rRmn(){ return rmn;}                //
};

////////////////////////////////////////////////////////////////////////
		 /*FUNCTION TO MODIFY A PARTICULAR ACCOUNT*/
////////////////////////////////////////////////////////////////////////

void modifyData(customer a,int n)
{ int found=0;
  char choice;
  long pos;
  fstream f;
  f.open("CUSTOMER.DAT",ios::binary|ios::in|ios::out);
  cout<<"Enter the customer no to edit :";
  cin>>n;
  while(!f.eof())
  { pos=f.tellg();
    f.read((char*)&a,sizeof(a));
    if(a.rCNo()==n)
    { a.showData();
      cout<<"\nAre you sure you want to modify this account(Y/N) : ";
      cin>>choice;
      choice=toupper(choice);
      if(choice=='N') return;
      a.readData();
      f.seekg(pos);
      f.write((char*)&a,sizeof(a));
      found++;
      break;
    }
  }
  if(found!=0) cout<<"\nChanges made .";
  else cout<<"\nAccount not found .";
  f.close();
}
////////////////////////////////////////////////////////////////////////
		 /*FUNCTION TO INSERT DATA*/
////////////////////////////////////////////////////////////////////////
void insertData(customer t)
{ int found=0;
  customer s;

  fstream fin,fout;

    fin.open("CUSTOMER.DAT",ios::binary|ios::in);
    fout.open("temp.DAT",ios::binary|ios::out);
    fin.read((char*)&s,sizeof(s));
    while(!fin.eof())
      { if(s.rCNo()<t.rCNo())
	  { fout.write((char*)&s,sizeof(s));
	    fin.read((char*)&s,sizeof(s));
	  }
	else
	  { fout.write((char*)&t,sizeof(t));
	    fout.write((char*)&s,sizeof(s));
	    found=1;
	    break;
	  }
      }
   if(!fin.eof())
     {  while(fin.read((char*)&s,sizeof(customer)))
	     {
	       fout.write((char*)&s,sizeof(customer));
	     }
     }
   if(found==0)  fout.write((char*)&t,sizeof(customer));
   fin.close(); fout.close();
   remove("CUSTOMER.DAT");
   rename("TEMP.DAT","CUSTOMER.DAT");
}


////////////////////////////////////////////////////////////////////////
	       /*FUNCTION TO DELETE AN ACCOUNT*/
////////////////////////////////////////////////////////////////////////

void deleteData(customer a,int n)
{ int found=0;
  char choice;
  cout<<"Enter the customer no. to delete : ";
  cin>>n;
  ifstream f1;
  f1.open("CUSTOMER.DAT",ios::binary);
  ofstream f2;
  f2.open("TEST.DAT",ios::binary);
  while(f1.read((char*)&a,sizeof(a)))
  { if(a.rCNo()!=n)
    { f2.write((char*)&a,sizeof(a));
    }
    else
    { cout<<"\ncNo"<<setw(10)<<"Name"<<setw(15)<<"Address"<<setw(25)
      <<"Mobile no"<<setw(11)<<"Last Read"<<setw(15)<<"Curr read";
      cout<<endl;
      a.showData();
      cout<<"\nDo you want to delete this account(Y/N) :";
      cin>>choice;
      choice=toupper(choice);
      if(choice!='Y')
      { f2.write((char*)&a,sizeof(a));
	found++;
      }
      found++;
    }
  }
   if(found==0)
  { cout<<"\nCustomer not found.";
  }
  else if(found==2) cout<<"\nCoustomer not deleted";
  else cout<<endl<<" Customer no. "<<n<<" deleted.";
  f1.close();
  f2.close();
  remove("CUSTOMER.DAT");
  rename("TEST.DAT","CUSTOMER.DAT");
}

////////////////////////////////////////////////////////////////////////
	/*FUNCTION TO GENERATE BILL FOR 1 ACCOUNT*/
////////////////////////////////////////////////////////////////////////

void mtnlBill(customer a,int n)
{ float ans;
  int found=0;
  fstream f;
  float bill(int,int);      //******PROTOTYPE FOR FUNCTION BILL*******
  cout<<"Enter the customer no :";
  cin>>n;
  f.open("CUSTOMER.DAT",ios::binary|ios::in|ios::out);
  while((found==0) && f.read((char*)&a,sizeof(a)))
  { if(a.rCNo()==n)
    { cout<<"Mtnl bill of "<<endl;
      cout<<"cNo"<<setw(10)<<"name"<<setw(15)<<"address"<<setw(25)
      <<"rmn"<<setw(11)<<"lReading"<<setw(15)<<"Creading"<<endl;
      a.showData();
      int l=a.rLReading();
      int c=a.rCReading();
      ans=bill(l,c);
      cout<<endl<<"\nTotal bill of client is : "<<ans<<" RS.";
      found++;
    }
  }
  if(found==0)
  { cout<<"\nCustomer not found";
    return;
  }

}
////////////////////////////////////////////////////////////////////////
	    /*FUNCTION TO ACCOUNT FOR MONEY*/
////////////////////////////////////////////////////////////////////////

float bill(int lr,int cr)
{ int reading=cr-lr+1;
  float money=0.0,temp=0.0;
  if(reading<201)money=reading;         // ***rs 1 for each call***
  else if(reading>201 && reading<401)   // ***rs .80 for each
  { temp=reading-200;                   // call after 200 calls***
    temp*=0.8;
    money=200+temp;
  }
  else                                  // ***rs .50 for each
  { temp=reading-400;                   // call after 400 calls***
    temp*=0.5;
    money=temp+360;
  }
  money+=money*0.18;                   //18% GST
  return(money);
}

////////////////////////////////////////////////////////////////////////
	  /*FUNCTION TO DISPLAY ALL DATA OF CUSTOMER*/
////////////////////////////////////////////////////////////////////////

void display()
{ customer a;
  ifstream f;
  f.open("CUSTOMER.DAT",ios::binary);
  cout<<"cNo"<<setw(10)<<"Name"<<setw(15)<<"Address"<<setw(25)
       <<"Mobile no"<<setw(11)<<"Last Read"<<setw(15)<<"Curr read";
  while(f.read((char*)&a,sizeof(a)))
  { cout<<endl;
    a.showData();
  }
  f.close();
}

////////////////////////////////////////////////////////////////////////
			    /*VOID MAIN*/
////////////////////////////////////////////////////////////////////////

void main()
{ customer c;
  int opt;
  do
  { clrscr();
    cout<<"  ##MAIN MENU##";
    cout<<"\n1.Insert data";
    cout<<"\n2.Modify data";
    cout<<"\n3.Delete data";
    cout<<"\n4.Generate bill";
    cout<<"\n5.Display data";
    cout<<"\n6.Exit";
    cout<<"\nEnter your choice :";
    cin>>opt;
    switch(opt)
    { case 1 :  c.readData();
		insertData(c);
		getch();
		break;
      case 2 :  int no2;
		modifyData(c,no2);
		getch();
		break;
      case 3 :  int no3;
		deleteData(c,no3);
		getch();
		break;
       case 4 : int no4;
		mtnlBill(c,no4);
		getch();
		break;
      case 5 :  display();
		getch();
		break;
      case 6 :  break;
      default : cout<<"Wrong choice";
		getch();
		break;
    }
  }while(opt!=6);

}
