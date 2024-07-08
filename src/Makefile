all: assembler

assembler: pass1.o pass2.o classes.o
	g++ pass1.o pass2.o classes.o -o assembler

pass1.o: pass1.cpp
	g++ -c pass1.cpp

pass2.o: pass2.cpp classes.h utilities.h
	g++ -c pass2.cpp

classes.o: classes.cpp classes.h
	g++ -c classes.cpp

clean:
	rm *.o assembler intermediate.txt listing.txt -f