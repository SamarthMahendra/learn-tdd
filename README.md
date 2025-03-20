# JEST tutorial for test-driven development
Learn how to write unit tests and other kinds of tests

# Setup

Install dependencies

`$ npm install`

Run tests

`$ NODE_ENV=test npx jest /path/to/test/file`

Run coverage

`$ NODE_ENV=test npx jest --coverage /path/to/test/file`

View coverage report in `coverage/lcov-report/index.html`

**Windows Note**: If you are on Windows and the above commands do not run
because of NODE_ENV not recognized then first set the environment variable from the terminal using `SET NODE_ENV=test` and then
run the jest command `npx jest --covereage /path/to/test/file`. The coverage is optional.

The followung database scripts are not necessary. If you still need
them for manual testing here they are:

`$ npx ts-node insert_sample_data.ts "mongodb://127.0.0.1:27017/my_library_db"`

Clean the database

`npx ts-node remove_db.ts "mongodb://127.0.0.1:27017/my_library_db"`

# Description

This repository illustrates how to use jest to write unit tests 
for a server in typescript. The examples are as follows:

- `tests/authorSchema.test.ts`: Unit tests to verify the schema of the authors colletion. 
- `tests/bookDetailsService.test.ts`: Unit tests to verify the behavior of the service that is used to retrieve the details of a particular book.
- `tests/createBookService.test.ts`: Unit tests to verify if a book is created successfully.

# For you to do

## Part 1

Write a unit test for the GET /authors service. 
The service should respond with a list of author names and lifetimes sorted by family name of the authors. It should respond
with a "No authors found" message when there are no authors in the database. If an error occurs when retrieving the authors then the
service responds with an error code of 500. The unit test
should be placed in `tests/authorService.test.ts`.

Test results:
```
PASS tests/authorService.test.ts
  Verify GET /authors
    ✓ should respond with a list of author names and lifetimes sorted by family name (17 ms)
    ✓ should respond with 'No authors found' when there are no authors in the database (2 ms)
    ✓ should respond with 'No authors found' and status 500 when there is an error retrieving authors (1 ms)
```

## Part 2

Briefly explain a limitation of the tests in `tests/authorSchema.test.ts` in the space below.

 -> limitation of the tests in `authorSchema.test.ts` is that, they only verify the schema validation and properties of the Author model in isolation, without testing the actual database interactions. 

These tests use `validateSync()` which only checks if the model conforms to the schema constraints, but doesn't test if the data is correctly saved to or retrieved from the database. For the statics methods like `getAuthorCount`, `getAllAuthors`, and `getAuthorIdByName`, the tests are mocking MongoDB methods (`countDocuments`, `find`, `findOne`) rather than testing against a real database, which means they don't verify if the queries are correctly formed for the actual database structure or if the methods work with real MongoDB instances.

## Part 3

Generate the coverage report for the tests you wrote. How can you improve
your tests using the coverage report? Briefly explain your 
process in the space below.

Coverage report results:
```
--------------------|---------|----------|---------|---------|--------------------------------
File                | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s              
--------------------|---------|----------|---------|---------|--------------------------------
All files           |   47.34 |     5.71 |    5.88 |   48.74 |                                
 learn-tddv2        |   67.85 |        0 |       0 |   67.85 |                                
  server.ts         |   67.85 |        0 |       0 |   67.85 | 25-37                          
 learn-tddv2/models |   38.09 |        0 |       0 |   38.55 |                                
  author.ts         |   27.27 |        0 |       0 |   28.12 | 56-63,74-82,92,102-109,119-123 
  book.ts           |      40 |        0 |       0 |      40 | 72,83-88,97,110-120            
  bookinstance.ts   |   46.66 |        0 |       0 |   46.66 | 50-53,63-70,80                 
  genre.ts          |   54.54 |        0 |       0 |   54.54 | 39,48-52                       
 learn-tddv2/pages  |   49.47 |    22.22 |    12.5 |   52.27 |                                
  authors.ts        |     100 |      100 |     100 |     100 |                                
  book_details.ts   |    37.5 |        0 |       0 |      40 | 17-36                          
  books.ts          |   36.84 |      100 |       0 |   41.17 | 9-16,25-29                     
  books_status.ts   |      50 |      100 |       0 |   55.55 | 13-18                          
  create_book.ts    |   47.05 |        0 |       0 |      50 | 22-32                          
  home.ts           |      40 |      100 |       0 |    42.1 | 15-30,39-44                    
--------------------|---------|----------|---------|---------|--------------------------------
```

Based on the coverage report, I can identify several areas for improvement:

1. **Low coverage in author.ts model**: The coverage report shows low coverage for the author model (only 28.12% line coverage). Specifically, the virtual properties (`name` and `lifespan`) and static methods are not fully tested. I could improve coverage by:
   - Adding more test cases for the `name` virtual, particularly with edge cases like non-ASCII characters
   - Adding direct tests for the `lifespan` virtual with various date combinations
   - Testing the static methods with actual MongoDB connections using a test database

2. **Integration testing**: My current tests for the `/authors` endpoint are unit tests that mock the `Author.getAllAuthors` method. To improve test quality, I could add integration tests that:
   - Use a test database to insert sample author data
   - Call the endpoint and verify it returns the correct data without mocking
   - Test how the endpoint handles malformed requests or database connection issues
