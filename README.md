# ReadWriteDlm2 
### CSV IO Supporting Decimal Comma, Date, DateTime, Time, Complex and Rational
[![ReadWriteDlm2](http://pkg.julialang.org/badges/ReadWriteDlm2_0.5.svg)](http://pkg.julialang.org/?pkg=ReadWriteDlm2) [![ReadWriteDlm2](http://pkg.julialang.org/badges/ReadWriteDlm2_0.6.svg)](http://pkg.julialang.org/?pkg=ReadWriteDlm2) [![Build Status](https://travis-ci.org/strickek/ReadWriteDlm2.jl.svg?branch=master)](https://travis-ci.org/strickek/ReadWriteDlm2.jl)   [![Build status](https://ci.appveyor.com/api/projects/status/ojv8nnuw63kh9yba/branch/master?svg=true)](https://ci.appveyor.com/project/strickek/readwritedlm2-jl/branch/master)  [![codecov.io](http://codecov.io/github/strickek/ReadWriteDlm2.jl/coverage.svg?branch=master)](http://codecov.io/github/strickek/ReadWriteDlm2.jl?branch=master)

`readdlm2()`, `writedlm2()`, `readcsv2()` and `writecsv2()` have additional support for `Date`, `DateTime`, `Time`, 
`Complex` and `Rational` types. 

For "decimal dot" users the functions `readcsv2()` and `writecsv2()` have the right defaults: Delimiter is `','` (fixed) and `decimal='.'`. Default Type `Any` aktivates parsing for all Types.

The basic idea of `readdlm2(), writedlm2()` is to support the "decimal comma countries". This functions use 
`';'` as default delimiter and `','` as default decimal mark. "decimal dot" users of this functions have to define 
delimiter and `decimal='.'`. 

Support for `Time`, `Complex` and `Rational` and the functions `readcsv2()`/`writecsv2()` start with Version for Julia 0.6.
For documentation of `ReadWriteDlm2` for Julia 0.5 see: https://github.com/strickek/ReadWriteDlm2.jl/blob/v0.3.1/README.md

### Installation
This package is registered and can be installed with:
```
Pkg.add("ReadWriteDlm2")
```

### Basic Examples: How To Use `ReadWriteDlm2`
```
julia> using ReadWriteDlm2                        # make readdlm2, readcsv2, writedlm2 and writecsv2 available

julia> A = Any[1 1.2; "text" Date(2017)];         # test array with: Int, Float64, String and Date type
julia> writedlm2("test1.csv", A)                  # test1.csv: "1;1,2\ntext;2017-01-01\n"
julia> readdlm2("test1.csv")                      # read `CSV` data: All four types are parsed correctly!
2×2 Array{Any,2}:
 1        1.2
  "text"   2017-01-01
  
julia> B = Any[1 complex(1.5,2.7);1.0 1//3];      # test array with: Int, Complex, Float64 and Rational type
julia> writecsv2("test2.csv", B)                  # test2.csv: "1,1.5 + 2.7im\n1.0,1//3\n"
julia> readcsv2("test2.csv")                      # read CSV data: All four types are parsed correctly!
2×2 Array{Any,2}:
 1    1.5+2.7im
 1.0    1//3 
```

## Function `readdlm2()`
Read a matrix from `source`. The `source` can be a text file, stream or byte array. Each line, separated
by `eol` (default is `'\n'`), gives one row. The columns are separated by `';'`, another `delim` can be defined. 

    readdlm2(source; options...)
    readdlm2(source, T::Type; options...)
    readdlm2(source, delim::Char; options...)
    readdlm2(source, delim::Char, T::Type; options...)
    readdlm2(source, delim::Char, eol::Char; options...)
    readdlm2(source, delim::Char, T::Type, eol::Char; options...)

Pre-processing of `source` with regex substitution changes the decimal marks from `d,d` to `d.d`.
For default `rs` the keyword argument `decimal=','` sets the decimal Char in the `r`-string of `rs`.
When a special regex substitution tuple `rs=(r.., s..)` is defined, the argument `decimal` is not used.
Pre-processing can be switched off with: `rs=()`.

In addition to Base `readdlm()`, strings are also parsed for `Dates` (default formats are ISO) and
`Time` format `HH:MM[:SS[.s{1,9}]]`. To switch off parsing Dates/Time set: `dfs="", dtfs=""`.
`locale` defines the language of day (`E`, `e`) and month (`U`, `u`) names.

If all data is numeric, the result will be a numeric array. In other cases
a heterogeneous array of numbers, dates and strings is returned. To include parsing for `Complex` and 
`Rational` numbers, use `Any` as Type argument. Homogeneous arrays are supported for Type arguments:
`Bool`, `Int`, `Float64`, `Complex`, `Rational`, `DateTime`, `Date` and `Time`.

### Additional Keyword Arguments `readdlm2()`
* `decimal=','`: decimal mark Char used by default `rs`, irrelevant if `rs`-tuple is not the default one
* `rs=(r"(\d),(\d)", s"\1.\2")`: [regular expression](https://docs.julialang.org/en/stable/manual/strings/#Regular-Expressions-1) (r, s)-tuple, change d,d to d.d if `decimal=','`
* `dtfs="yyyy-mm-ddTHH:MM:SS"`: [format string](https://docs.julialang.org/en/stable/stdlib/dates/#Base.Dates.DateFormat) for DateTime parsing, default is ISO
* `dfs="yyyy-mm-dd"`: [format string](https://docs.julialang.org/en/stable/stdlib/dates/#Base.Dates.DateFormat) for Date parsing, default is ISO
* `locale="english"`: language for parsing dates names, default is english

### Function `readcsv2()`

    readcsv2(source, T::Type=Any; opts...)

Equivalent to `readdlm2()` with delimiter `','` and `decimal='.'`. Default Type `Any` includes parsing
of `Bool`, `Int`, `Float64`, `Complex`, `Rational`, `DateTime`, `Date` and `Time`.

### Documentation For Base `readdlm()`
More information about Base functionality and (keyword) arguments - which are also 
supported by `readdlm2()` and `readcsv2()` - is available in the 
[stable documentation for readdlm()](https://docs.julialang.org/en/stable/stdlib/io-network/#Base.DataFmt.readdlm-Tuple{Any,Char,Type,Char}). 

### Compare Default Functionality `readdlm()` - `readdlm2()` - `readcsv2()`
| Module        | Function               | Delimiter  | Dec.Mark  | Date(Time)   | Complex, Rational |
|:------------- |:-----------------------|:----------:|:---------:|:-------------|:------------------|
| Base.DataFmt  | `readdlm()`            | `' '`      | `'.'`     | n.a.(String) | n.a.(String)      |
| ReadWriteDlm2 | `readdlm2()`           | `';'`      | `','`     | parse ISO    | Optional (Type)   |
| ReadWriteDlm2 | `readcsv2()`           | `','`      | `'.'`     | parse ISO    | Default (Type=Any)|

-> `readdlm2(source, ' ', decimal='.', dfs="", dtfs="")` gives the same result as `readdlm(source)`.

### Example `readdlm2()`
Read the Excel (lang=german) text-file `test_de.csv` and store the array in `data`:
```
data = readdlm2("test_de.csv", dfs="dd.mm.yyyy", dtfs="dd.mm.yyyy HH:MM")
```



## Function `writedlm2()`
Write `A` (a vector, matrix, or an iterable collection of iterable rows) as text to `f` 
(either a filename string or an IO stream). The columns will be separated by `';'`,
another `delim` (Char or String) can be defined.

    writedlm2(f::IO, A; options...)
    writedlm2(f::IO, A, delim; options...)
    writedlm2(f::AbstractString, A; options...)
    writedlm2(f::AbstractString, A, delim; options...)

By default, a pre-processing of floats takes place. Floats are parsed to strings
with decimal mark changed from `'.'` to `','`. With a keyword argument
another decimal mark can be defined. To switch off this pre-processing set: `decimal='.'`.

Base `writedlm()` writes `3000.0` always short as `3e3`. To keep type information
`writedlm2()` writes long like print() by default. Set `write_short=true` to have 
the same behaviour as in Base `writedlm()`.

In `writedlm2()` the output format for `Date` and `DateTime` data can be defined with format strings.
Defaults are the ISO formats. Day (`E`, `e`) and month (`U`, `u`) names are written in
the `locale` language. For writing `Complex` numbers the imaginary component suffix can be changed with the
`imsuffix=` keyword argument.

### Additional Keyword Arguments `writedlm2()`
* `decimal=','`: Character for writing decimal marks, default is a comma
* `write_short=false`: Bool - use print() to write data, set `true` for print_shortest()
* `dtfs="yyyy-mm-ddTHH:MM:SS"`: [format string](https://docs.julialang.org/en/stable/stdlib/dates/#Base.Dates.DateFormat),  DateTime write format, default is ISO
* `dfs="yyyy-mm-dd"`: [format string](https://docs.julialang.org/en/stable/stdlib/dates/#Base.Dates.DateFormat), Date write format, default is ISO
* `locale="english"`: language for writing dates names, default is english
* `imsuffix="im"`: Complex - imaginary component suffix `"i"`, `"j"` or `"im"`(=default)

### Function `writecsv2()`

    writecsv2(filename, A; opts...)
    
Equivalent to `writedlm2` with fix delimiter `','` and `decimal='.'`. 

### Documentation For Base `writedlm()`
More information about Base functionality - which is also 
supported by `writedlm2()` and `writecsv2()` - is available in the 
[stable documentation for writedlm()](https://docs.julialang.org/en/stable/stdlib/io-network/#Base.DataFmt.writedlm).

### Compare Default Functionality `writedlm()` - `writedlm2()` - `writecsv2()`
| Module        | Function           | Delimiter | Dec.Mark | Date(Time) | Write Numbers    |
|:------------- |:------------------ |:---------:|:--------:|:---------- |:-----------------|
| Base.DataFmt  | `writedlm()`       | `'\t'`    | `'.'`    | ISO-Format | print_shortest() |
| ReadWriteDlm2 | `writedlm2()`      | `';'`     | `','`    | ISO-Format | like print()     |
| ReadWriteDlm2 | `writecsv2()`      | `','`     | `'.'`    | ISO-Format | like print()     |

-> `writedlm2(f, A, '\t', decimal='.', write_short=true)`  gives the same result as  `writedlm(f, A)`.

### Example `writedlm2()`
Write Julia `data` to text-file `test_de.csv`, readable by Excel (lang=german):
```
writedlm2("test_de.csv", data, dtfs="dd.mm.yyyy HH:MM", dfs="dd.mm.yyyy")
```
