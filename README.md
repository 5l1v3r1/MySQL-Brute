
# MySQL Brute


### MySQL brute-forcer.

##### MySQL Brute v.0.10


[1]: https://tinram.github.io/images/mysqlbrute.png
![mysqlbrute][1]


## Purpose

Brute force a MySQL user using a wordlist file.

MySQL Brute was created for localhost account recovery: where the root account is inaccessible and an ordinary user password is lost. PLESK-managed MySQL is a candidate.


## OS

+ Linux


## Usage

    ./mysqlbrute --help

    ./mysqlbrute -h <host> -u <username> -f <wordlist_file>

    ./mysqlbrute -h localhost -u wordpress -f top_100000.txt

There are many wordlists available e.g. [Daniel Miessler's](https://github.com/danielmiessler/SecLists/tree/master/Passwords).

Alternatively a simple list for testing is the Linux dictionary (Debian path):

    ./mysqlbrute -h localhost -u <username> -f /usr/share/dict/words

Other options:

    -p <port_number>


## Speed

MySQL Brute's speed bottlenecks are:

+ MySQL connect ( *mysql\_real\_connect()* ),
+ MySQL spawning only a limited number of threads for connections,
+ if not a localhost connection, the network connection (MySQL localhost connection uses a socket instead of TCP/IP).

MySQL Brute churns through approximately 50,000 passwords per second (vanilla Core i3 desktop CPU) on a localhost socket connection - considerably faster than the Bash and Python scripts I tried before creating MySQL Brute (and curiously, faster than the multi-threaded *Hydra*). However, when using a network connection, MySQL Brute is much slower.

MySQL Brute can be quite easily converted to multi-threading with the OMP library. On an intermediate-sized wordlist, the OMP version was 3 seconds faster on the same machine. However, with MySQL connections being the bottleneck, and some program instability on large wordlists, I abandoned multi-threading.


## Binaries

Download from [Releases](https://github.com/Tinram/MySQL-Brute/releases).


## Build

### Linux-only

Ensure the *libmysqlclient-dev* library (from the repo) is installed:

    locate libmysqlclient-dev

If `locate` does not find the library, install on Debian-based distros with:

    sudo apt-get install libmysqlclient-dev

In the directory containing either the clone or the extracted zip files, compile with GCC:

    make

or full process:

    make && make install

compile manually:

**GCC:**

    gcc mysqlbrute.c $(mysql_config --cflags) $(mysql_config --libs) -o mysqlbrute -Ofast -Wall -Wextra -Wuninitialized -Wunused -Werror -std=gnu99 -s

**Clang:**

    clang mysqlbrute.c $(mysql_config --cflags) $(mysql_config --libs) -o mysqlbrute -O3 -Wall -Wextra -Wuninitialized -Wunused -Werror -std=gnu99 -s


## Other

### Warning

MySQL Brute will rapidly enlarge the MySQL error log file ( */var/log/mysql/error.log* for Debian-based distros).

### Location

It's more convenient for MySQL Brute to be available from any directory location via the PATH system variable (rather than copying the executable file to the directory where needed).

    make install

Or move the *mysqlbrute* executable to a location such as */usr/local/bin* (location must be present in $PATH).


## Hydra Comparison

    hydra -l wordpress -P top_100000.txt -t 4 -F localhost mysql

(As per the example in **Usage**, using 4 threads, ~1,050 tries per second on a Core i3.)


## Credits

+ MMxM: Bash script that inspired.
+ Vivek Gite: MySQL libraries compiling.
+ Tim Čas: elegant EOL removal.
+ Ben Alpert: microsecond timer.


## License

MySQL Brute is released under the [GPL v.3](https://www.gnu.org/licenses/gpl-3.0.html).
