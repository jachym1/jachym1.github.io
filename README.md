# pyc-hdb Python compiled HANA client

Procedure to compile pure SAP Python client for HANA Database to C binary executable to speed up program execution.

Install Python and Nuitka package from http://nuitka.net/pages/download.html Edit fibers.h Nuitka header located in site-packages/nuitka/build/include/nuitka to avoid error during compilation (missing type)

Add line

    #include <stdint.h>

Install latest py-HDB client version from git epository git clone https://github.com/SAP/pyhdb.git

Edit pyhdb/pyhdb/protocol/types.py to avoid NoneType error during compilation

Comment lines:

Begin of code

    class NoneType(Type):

    python_type = None.__class__

    @classmethod
    def to_sql(cls, _):

    return text_type("NULL")

    @classmethod

    def prepare(cls, type_code):

    return struct.pack('<B', type_code | 0x80)

End of code

cd pyhdb
Edit __main__.py according to your needs - HANA connection parameters and sql statements fro example

    import pyhdb
    connection = pyhdb.connect(
    host="demo",
    port=30015,
    user="SYSTEM",
    password="demo"
    )

    cursor = connection.cursor()
    cursor.execute("SELECT TEST FROM TEST")
    result = cursor.fetchall()
    print result
    connection.close()

Compile and run the program

    nuitka . --recurse-all
    mv ..exe test
    ./test

Enjoy
 
