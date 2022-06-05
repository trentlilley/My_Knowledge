# Table of Contents

# Overview
This document goes over the various ways to read and write data from various file types. It also goes over various methods on how to handle file reading errors and how to read gigabyte scale files more efficiently.

**DISCLAIMER**: This file was purely created to be my personal reference and serve as a comprehensive record of the knowledge I gained. As such, it is not intended to be a 100% correct reference on these topics and there may be errors throughout the text. It was written in a way to facilitate learning however, and some examples may come from various  textbooks, courses, videos, discussion forums, and online articles that I came across while studying this subject. 
<br></br>

## Reading Bytes From a Data File
- great for reading data from a .dat file.
- use a `DataInputStream` as your reader and use the `readByte()` function to read a single byte from the stream
- to stop reading once the end of the file is reached, this example uses a catch statement that catches an `end of file exception`

```Java
String filename = args[0];
DataInputStream in = null;
byte databyte = 0;

try {
  while (true) {
    in = new DataInputStream(new FileInputStream(fileName));
    databyte = in.readByte();
    WriteOutput(databyte);
  }
}
catch (EOFException eofErr) {
  System.out.println("End of " + filename + " reached");

  try {
    in.close();
  }
  catch (Exception closeErr) {
    System.out.println("Problem closing input data file" + closeErr);
  }
  
}
catch (IOException ioEx) {
  System.out.println("Problem reading input file" + ioEx);
}

```

```Java
int MeasurementLength = 8;		// This is the length of all measurements (including time) in bytes
int IdLength = 4;				// This is the length of IDs in the byte stream
byte databyte = 0;				// This is the data byte read from the stream
int bytesread = 0;				// This is the number of bytes read from the stream
long measurement;				// This is the word used to store all measurements - conversions are illustrated.
int id;							// This is the measurement id
int i;							// This is a loop counter


/* Appending the 4 bytes comprising the id to the int id */
id = 0;
	for (i=0; i<IdLength; i++ )
				{
					databyte = ReadFilterInputPort();	// This is where we read the byte from the stream...
					id = id | (databyte & 0xFF);		// We append the byte on to ID... 0xFF is 11111111
					if (i != IdLength-1)				// If this is not the last byte, then slide the
					{									// previously appended byte to the left by one byte
						id = id << 8;					// to make room for the next byte we append to the ID
					}
					bytesread++;						// Increment the byte count
				}

/* Getting the 8 byte 'measurement'. For IDs of 1 this is a long, for IDs 2-5 this is a double
 *  
 */
	measurement = 0;
				for (i=0; i<MeasurementLength; i++ )
				{
					databyte = ReadFilterInputPort();
					measurement = measurement | (databyte & 0xFF);	// We append the byte on to measurement...
					if (i != MeasurementLength-1)					// If this is not the last byte, then slide the
					{												// previously appended byte to the left by one byte
						measurement = measurement << 8;				// to make room for the next byte we append to the
																	// measurement
					}
					bytesread++;									// Increment the byte count
				}
```