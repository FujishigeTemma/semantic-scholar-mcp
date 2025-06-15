# Semantic Scholar MCP Server

A Model Context Protocol (MCP) server that provides access to Semantic Scholar's academic paper database through their API.

## Features

- **Paper Search**: Search for academic papers with filters for year, fields of study, and open access
- **Paper Details**: Get comprehensive information about specific papers including abstracts, authors, and citation counts
- **Author Information**: Retrieve detailed author data including affiliations, h-index, and citation metrics
- **Citation Export**: Generate citations in multiple formats (BibTeX, APA, MLA, Chicago)

## Usage

### Get API Key

While the Semantic Scholar API can be used without authentication, having an API key provides higher rate limits. To get an API key:

1. Visit [Semantic Scholar API](https://www.semanticscholar.org/product/api)
2. Request an API key

### Add to Claude Code configuration

Run the following command to add the Semantic Scholar MCP server to your project-scope Claude Code configuration:

```bash
claude mcp add semantic-scholar-mcp -s project -e SEMANTIC_SCHOLAR_API_KEY="your-api-key-here" -- uv run --with "git+https://github.com/FujishigeTemma/semantic-scholar-mcp" semantic-scholar-mcp serve
```

Or manually add it to your .mcp.json configuration file:

```json
{
  "mcpServers": {
    "semantic-scholar-mcp": {
      "type": "stdio",
      "command": "uv",
      "args": [
        "run",
        "--with",
        "git+https://github.com/FujishigeTemma/semantic-scholar-mcp",
        "semantic-scholar-mcp",
        "serve"
      ],
      "env": {
        "SEMANTIC_SCHOLAR_API_KEY": "your-api-key-here"
      }
    }
  }
}
```

## Available Tools

1. **search_paper** - Search for papers
   - Required: `query` (search terms)
   - Optional: `fields`, `limit`, `offset`, `year`, `fieldsOfStudy`, `openAccessPdf`

2. **get_paper** - Get detailed paper information
   - Required: `paper_id` (supports multiple ID types: DOI, ArXiv ID, S2 Paper ID, etc.)
   - Optional: `fields` (customize returned data, see: [Field Customization](#field-customization))

3. **get_authors** - Get author information for a paper
   - Required: `paper_id`
   - Optional: `fields`, `limit`, `offset`

4. **get_citation** - Generate formatted citations
   - Required: `paper_id`
   - Optional: `format` (bibtex, apa, mla, chicago)


## CLI Examples

Search for papers:
```bash
semantic-scholar-mcp tools search_paper "machine learning" --limit 5 --year "2020-2023"
```

Get paper details:
```bash
semantic-scholar-mcp tools get_paper "10.1038/nature12373"
```

Get authors for a paper:
```bash
semantic-scholar-mcp tools get_authors "649def34f8be52c8b66281af98ae884c09aef38b"
```

Generate BibTeX citation:
```bash
semantic-scholar-mcp tools get_citation "649def34f8be52c8b66281af98ae884c09aef38b" --format bibtex
```

## Field Customization

All tools support a `fields` parameter to customize the returned data. This allows you to request only the information you need, reducing response size and improving performance.

### Paper Fields (for search_paper and get_paper)

**Basic fields:**
- `paperId` - Unique paper identifier
- `title` - Paper title
- `abstract` - Paper abstract
- `year` - Publication year
- `publicationDate` - Full publication date

**Author information:**
- `authors` - List of authors (returns `authorId` and `name` by default)
- `authors.authorId` - Author's unique identifier
- `authors.name` - Author's name
- `authors.affiliations` - Author's institutional affiliations
- `authors.citationCount` - Author's total citation count
- `authors.hIndex` - Author's h-index

**Citation and reference data:**
- `citationCount` - Number of times this paper has been cited
- `referenceCount` - Number of references in this paper
- `citations` - List of papers that cite this paper
- `references` - List of papers referenced by this paper

**Publication details:**
- `journal` - Journal information (name, volume, pages, etc.)
- `venue` - Publication venue
- `publicationTypes` - Types of publication (e.g., JournalArticle, Conference)
- `fieldsOfStudy` - Academic fields (e.g., Computer Science, Medicine)
- `s2FieldsOfStudy` - Semantic Scholar's field classifications

**Additional metadata:**
- `doi` - Digital Object Identifier
- `arxivId` - ArXiv identifier
- `url` - Paper URL
- `openAccessPdf` - Open access PDF information
- `embedding` - Paper embedding vectors (for similarity analysis)

### Author Fields (for get_authors)

- `authorId` - Unique author identifier
- `name` - Author's name
- `affiliations` - Institutional affiliations
- `citationCount` - Total citation count
- `hIndex` - h-index metric
- `paperCount` - Number of papers published
- `url` - Author's profile URL

### Example Field Usage

Get basic paper information:
```bash
semantic-scholar-mcp tools search_paper "machine learning" --fields "paperId,title,year,citationCount"
```

Get detailed paper with author affiliations:
```bash
semantic-scholar-mcp tools get_paper "10.1038/nature12373" --fields "title,abstract,authors.name,authors.affiliations,journal,year"
```

Get comprehensive author information:
```bash
semantic-scholar-mcp tools get_authors "649def34f8be52c8b66281af98ae884c09aef38b" --fields "authorId,name,affiliations,citationCount,hIndex,paperCount"
```

## Development

### Setting up the development environment

```bash
uv sync

uv run pytest tests/
uv run ruff format .
uv run ruff check . --fix
uv run ty check
```

### Project Structure

```
semantic-scholar-mcp/
  src/
    semantic_scholar_mcp/
      __init__.py
      server.py      # Main server implementation
      cli.py         # CLI interface
  tests/                 # Test files
  pyproject.toml        # Project configuration
  README.md            # This file
```

## API Rate Limits

- Without API key: 100 requests per 5 minutes
- With API key: 1 request per second (higher limits available on request)

## Supported Paper ID Types

The API supports various paper identifier formats:
- Semantic Scholar ID (e.g., "649def34f8be52c8b66281af98ae884c09aef38b")
- DOI (e.g., "10.1038/nature12373")
- ArXiv ID (e.g., "arXiv:2106.15928")
- MAG ID
- ACL ID
- PubMed ID
- Corpus ID

## License

MIT License

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Acknowledgments

This project uses the [Semantic Scholar API](https://www.semanticscholar.org/product/api) to access academic paper data.
