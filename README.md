# CSVParser
Simple single source file to parse CSV (comma-separated value) files

## Concept
It's a simple ANSI C file that can be used for importing CSV files. 
Whilst writing a CSV file is usually trivial, reading them can be hard
because of all the escape rules. Also a CSV file can have an optional
header. Usually this can be detected automatically because the header
is text data whilst the following rows are all numerical.

The whole CSV file is read in as one object, which you then query as a
table. This allows you to write programs which operate on arbitrary
CSV files.

## Example usage

| Name | Salary | Payroll Id |
|------|--------|------------|
| Joe Bloggs | 30000.0 | 1234 |
| John Smith | 32000.0 | 1235 |
| Jill Jones | 28000.0 | 1236 |

```

#include <stdio.h>
#include "csv.h"

int main(int argc, char** argv)
{
    int datatype[100];
    CSV* csv = loadcsv(argv[1]);
    int i, j;

    int Nrows, Ncolumns;
    csv_getsize(csv, &Ncolumns, &Nrows);
    for (i = 0; i < Ncolumns; i++)
    {
        const char* columnname = csv_column(csv, i, &datatype[i]);
        printf("Column %d name %s datatype %d\n", i, columnname, datatype[i]);
    }
    for (i = 0; i < Nrows; i++)
    {
        for (j = 0; j < Ncolumns; j++)
        {
            double value;
            const char* str;
            if (datatype[j] == CSV_REAL && csv_hasdata(csv, j, i))
            {
                value = csv_get(csv, j, i);
                printf("%f, ", value);
            }
            else if (datatype[j] == CSV_STRING && csv_hasdata(csv, j, i))
            {
                str = csv_getstr(csv, j, i);
                printf("\"%s\", ", str);
            }
        }
    }

    killcsv(csv);

    return 0;
}


```
