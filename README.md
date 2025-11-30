# Date parser

A parser library for dates from the TOML spec., which is a looser version of [RFC 3339 date & time specification](https://datatracker.ietf.org/doc/html/rfc3339) 

The library provides its own `Date` type, however there are function to (in slightly lossy fashion) convert `Date` to Odin's `datetime.DateTime` & a UTC timezone offset in hours.

This library is used in both my [main TOML parser](https://github.com/Up05/toml_parser) & [small round-trippable utoml](https://github.com/Up05/utoml) libraries.
So parts of it have been tested quite thoroughly.

# Usage

```sh
git clone https://github.com/Up05/date-parser
```

```odin
import "core:fmt"
import dates "date-parser"

main :: proc() {
    date, err := dates.from_string("1996-02-29 16:39:57-08:00")
    fmt.println(dates.to_string(date)) // prints: 1996-02-29 16:39:57-08:00 NONE

    assert(dates.is_date_lax("1996-02-29 doesn't matter")) // quickly determines if some
    assert(dates.is_date_lax("96:02:29   doesn't matter")) // string looks like a date
}
```

# Functions

```
from_string :: proc(date: string) -> (out: Date, err: DateError) {
```
Parses the date.

```
to_string :: proc(date: Date, time_sep := ' ') -> (out: string, err: DateError) { 
```
Formats the date as RFC3339.

```
partial_date_to_string :: proc(date: Date, time_sep := ' ') -> (out: string, err: DateError) {
```
Formats the date for TOML. _Only checks `is_date_only` & `is_time_only`._ 

```
combine :: proc(date, time: Date) -> Date {
```
Combines two partial dates.  
_Great for parsing single date & time after a tokenizer has split them up by space_

```
is_date_lax :: proc(date: string) -> bool {
```
Quickly check if a string looks like a date.

```
get_bad_spot :: proc(str: string, err: DateError) -> int {
```
Gets the index of the rune in the given string (which may have leading/following text) where the parser failed.

```
to_odin_datetime :: proc(date: Date) -> (result: datetime.DateTime, utc_offset: int, error: datetime.Error) {
```
Convert the library's `Date` type to Odin's standard(-ish) `core:time/datetime.DateTime` & a `utc_offset` (in hours).
_This may then be converted into other standard date or time types._ 

```
from_odin_datetime :: proc(dt: datetime.DateTime, utc_offset: int) -> Date {
```
Convert Odin's `DateTime` back into library's `Date`.
