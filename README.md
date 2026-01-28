# FindThatBook

AI-Powered Book Search - InfoTrack Technical Assessment

## Overview

FindThatBook is a .NET 8 Web API that uses AI (Google Gemini) to extract structured information from "dirty" book search queries and matches them against the Open Library API. It returns up to 5 book candidates with detailed explanations of why each book matched.

## Features

- **AI-Powered Query Extraction**: Uses Google Gemini to extract title, author, year, and keywords from unstructured queries
- **Open Library Integration**: Searches the Open Library API for matching books
- **Smart Matching Algorithm**: Applies a hierarchical matching system (Exact > Strong > Partial > Weak)
- **Match Explanations**: Provides detailed explanations for why each book was matched
- **Simple Web UI**: HTML/Tailwind frontend for easy testing

## Architecture

```
FindThatBook/
├── src/
│   ├── FindThatBook.Domain/          # Entities, Interfaces, Enums, Value Objects
│   ├── FindThatBook.Application/      # DTOs, Services, Use Cases
│   ├── FindThatBook.Infrastructure/   # External API integrations (Gemini, Open Library)
│   └── FindThatBook.API/             # Controllers, Configuration, wwwroot
└── tests/
    └── FindThatBook.Tests/           # Unit tests with xUnit
```

### Clean Architecture Layers

1. **Domain Layer**: Core business entities and interfaces
   - `Book`, `BookCandidate` entities
   - `ExtractedBookInfo` value object
   - `MatchStrength` enum
   - Service interfaces

2. **Application Layer**: Business logic and orchestration
   - `SearchBooksUseCase`: Main use case orchestrating the search flow
   - `BookMatchingService`: Implements matching algorithm
   - DTOs for API communication

3. **Infrastructure Layer**: External service integrations
   - `GeminiExtractionService`: Gemini AI integration for query extraction
   - `OpenLibrarySearchService`: Open Library API integration

4. **API Layer**: Web API and frontend
   - `BooksController`: REST API endpoints
   - Static HTML/Tailwind frontend

## Prerequisites

- .NET 8 SDK
- Google Gemini API Key (free tier available at [Google AI Studio](https://aistudio.google.com/))

## Setup Instructions

### 1. Clone the Repository

```bash
git clone <repository-url>
cd FindThatBook
```

### 2. Configure Gemini API Key

Option A: Edit `appsettings.Development.json`:
```json
{
  "Gemini": {
    "ApiKey": "YOUR_GEMINI_API_KEY_HERE"
  }
}
```

Option B: Use environment variable:
```bash
set Gemini__ApiKey=YOUR_GEMINI_API_KEY_HERE
```

Option C: Use .NET User Secrets (recommended):
```bash
cd src/FindThatBook.API
dotnet user-secrets init
dotnet user-secrets set "Gemini:ApiKey" "YOUR_GEMINI_API_KEY_HERE"
```

### 3. Build the Solution

```bash
dotnet build
```

### 4. Run Tests

```bash
dotnet test
```

### 5. Run the Application

```bash
cd src/FindThatBook.API
dotnet run
```

The application will start at:
- HTTP: http://localhost:5000
- HTTPS: https://localhost:5001

### 6. Access the Application

- **Web UI**: Open http://localhost:5000 in your browser
- **Swagger**: http://localhost:5000/swagger
- **API**: POST http://localhost:5000/api/books/search

## API Usage

### Search Books

**Endpoint**: `POST /api/books/search`

**Request Body**:
```json
{
  "query": "tolkien hobbit illustrated 1937",
  "maxResults": 5
}
```

**Response**:
```json
{
  "originalQuery": "tolkien hobbit illustrated 1937",
  "extractedInfo": {
    "title": "The Hobbit",
    "author": "J.R.R. Tolkien",
    "keywords": ["illustrated"],
    "year": 1937
  },
  "candidates": [
    {
      "key": "/works/OL262758W",
      "title": "The Hobbit",
      "authors": ["J.R.R. Tolkien"],
      "firstPublishYear": 1937,
      "coverUrl": "https://covers.openlibrary.org/b/id/...",
      "openLibraryUrl": "https://openlibrary.org/works/OL262758W",
      "matchStrength": "Exact",
      "matchScore": 95.5,
      "matchExplanation": "Exact match: Title exact match; Author exact match; Year exact match",
      "matchReasons": [
        "Title exact match: \"The Hobbit\"",
        "Author exact match: \"J.R.R. Tolkien\"",
        "Year exact match: 1937"
      ]
    }
  ],
  "totalCandidates": 1,
  "processingTime": "00:00:01.234"
}
```

## Example Queries

| Dirty Query | Extracted Info |
|------------|----------------|
| "mark huckleberry" | Title: The Adventures of Huckleberry Finn, Author: Mark Twain |
| "tolkien hobbit illustrated 1937" | Title: The Hobbit, Author: J.R.R. Tolkien, Year: 1937, Keywords: illustrated |
| "orwell 1984" | Title: 1984, Author: George Orwell |
| "rowling philosopher stone" | Title: Harry Potter and the Philosopher's Stone, Author: J.K. Rowling |

## Matching Algorithm

The matching system uses a weighted scoring approach:

| Component | Weight |
|-----------|--------|
| Title | 40% |
| Author | 35% |
| Keywords | 15% |
| Year | 10% |

### Match Strength Levels

- **Exact**: Both title and author match exactly (95%+ similarity)
- **Strong**: High confidence on both title and author (70%+ on both)
- **Partial**: Good match on either title OR author (60%+)
- **Weak**: Some keywords or partial matches found

## Configuration Options

### appsettings.json

```json
{
  "Gemini": {
    "ApiKey": "",
    "Model": "gemini-1.5-flash",
    "BaseUrl": "https://generativelanguage.googleapis.com/v1beta"
  },
  "OpenLibrary": {
    "BaseUrl": "https://openlibrary.org",
    "TimeoutSeconds": 30
  }
}
```

## Testing

The project includes comprehensive unit tests:

```bash
# Run all tests
dotnet test

# Run with verbose output
dotnet test --verbosity normal

# Run specific test class
dotnet test --filter "FullyQualifiedName~BookMatchingServiceTests"
```

### Test Coverage

- `BookMatchingServiceTests`: Tests for matching algorithm
- `SearchBooksUseCaseTests`: Tests for the main use case orchestration
- `BookCandidateTests`: Tests for domain entities
- `ExtractedBookInfoTests`: Tests for value objects

## Technologies Used

- .NET 8
- ASP.NET Core Web API
- Google Gemini AI (gemini-1.5-flash)
- Open Library API
- xUnit + Moq + FluentAssertions
- Tailwind CSS (via CDN)

## License

This project was created as a technical assessment for InfoTrack.
