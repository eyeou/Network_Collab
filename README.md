# ArXiv Entity-Relationship Visualization

A powerful tool for visualizing and querying relationships between ArXiv paper authors and their affiliations using Claude AI, Neo4j graph database, and MCP (Model Context Protocol) servers.

## 🎯 Overview

This project leverages the power of graph databases and AI to create interactive visualizations of academic relationships from ArXiv papers. By combining multiple MCP servers with Claude AI, we can extract, process, and visualize complex relationships between researchers, their affiliations, and their collaborative networks.

## 🚀 Demo
https://github.com/eyeou/Network_Collab/raw/refs/heads/main/reduced_demo.m4v

## ✨ Features

- **Entity-Relationship Visualization**: Interactive graph visualization of authors, papers, and affiliations
- **Custom Query Interface**: Natural language queries powered by Claude AI
- **Multi-format Support**: Process ArXiv papers in various formats including PDF
- **Real-time Analysis**: Live data extraction and relationship mapping
- **Graph Database Integration**: Efficient storage and querying with Neo4j
- **Author Network Analysis**: Discover collaboration patterns and research communities

## 🏗️ Architecture

The system integrates three key MCP servers with Claude AI:

```
┌─────────────┐    ┌──────────────┐    ┌─────────────┐
│   ArXiv     │    │   PDF        │    │   Neo4j     │
│   MCP       │────│   Reader     │────│   MCP       │
│   Server    │    │   MCP        │    │   Server    │
└─────────────┘    └──────────────┘    └─────────────┘
       │                   │                   │
       └───────────────────┼───────────────────┘
                           │
                   ┌───────────────┐
                   │   Claude AI   │
                   │   Orchestrator│
                   └───────────────┘
```

## 🚀 Getting Started

### Prerequisites

- Python 3.8+
- Claude Dekstop
- Neo4j Database (local or cloud instance)
- Claude AI API access
- MCP-compatible environment

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/arxiv-entity-visualization.git
   cd arxiv-entity-visualization
   ```

2. **Install MCP servers**
   
   Install the ArXiv MCP server:
   ```bash
   pip install git+https://github.com/prashalruchiranga/arxiv-mcp-server.git
   ```
   
   Install the Neo4j MCP server:
   ```bash
   pip install git+https://github.com/neo4j-contrib/mcp-neo4j.git
   ```
   
   Install the PDF Reader MCP server:
   ```bash
   pip install git+https://github.com/sylphxltd/pdf-reader-mcp.git
   ```

3. **Configure Neo4j**
   ```bash
   # Set up your Neo4j connection
   export NEO4J_URI="bolt://localhost:7687"
   export NEO4J_USER="neo4j"
   export NEO4J_PASSWORD="your_password"
   ```

4. **Configure MCP servers**
   Create a configuration file `mcp_config.json`:
   ```json
   {
     "servers": {
       "arxiv": {
         "command": "python",
         "args": ["-m", "arxiv_mcp_server"]
       },
       "neo4j": {
         "command": "python",
         "args": ["-m", "mcp_neo4j_cypher"],
         "env": {
           "NEO4J_URI": "bolt://localhost:7687",
           "NEO4J_USER": "neo4j",
           "NEO4J_PASSWORD": "your_password"
         }
       },
       "pdf_reader": {
         "command": "python",
         "args": ["-m", "pdf_reader_mcp"]
       }
     }
   }
   ```

### Usage

1. **Start the MCP servers**
   ```bash
   # The MCP servers will be automatically started when Claude connects
   ```

2. **Initialize the graph database**
   ```cypher
   // Create constraints for better performance
   CREATE CONSTRAINT author_name IF NOT EXISTS FOR (a:Author) REQUIRE a.name IS UNIQUE;
   CREATE CONSTRAINT paper_id IF NOT EXISTS FOR (p:Paper) REQUIRE p.arxiv_id IS UNIQUE;
   CREATE CONSTRAINT affiliation_name IF NOT EXISTS FOR (af:Affiliation) REQUIRE af.name IS UNIQUE;
   ```

3. **Query papers and build relationships**
   Use Claude AI to query ArXiv and populate the graph:
   ```
   "Find papers about 'machine learning' from the last 6 months and create a knowledge graph of authors and their affiliations"
   ```

## 📊 Example Queries

### Natural Language Queries (via Claude)
- "Show me the collaboration network for authors working on transformer models"
- "Find the most prolific authors in computer vision and their institutional affiliations"
- "Visualize the research connections between MIT and Stanford in AI papers"

### Direct Cypher Queries
```cypher
// Find top collaborating author pairs
MATCH (a1:Author)-[:AUTHORED]->(p:Paper)<-[:AUTHORED]-(a2:Author)
WHERE a1.name < a2.name
WITH a1, a2, count(p) as collaborations
ORDER BY collaborations DESC
LIMIT 10
RETURN a1.name, a2.name, collaborations;

// Find most connected institutions
MATCH (af:Affiliation)<-[:AFFILIATED_WITH]-(a:Author)-[:AUTHORED]->(p:Paper)
WITH af, count(DISTINCT p) as paper_count
ORDER BY paper_count DESC
LIMIT 10
RETURN af.name, paper_count;
```

## 🛠️ MCP Servers

### ArXiv MCP Server
- **Repository**: https://github.com/prashalruchiranga/arxiv-mcp-server
- **Purpose**: Fetches paper metadata, abstracts, and author information from ArXiv
- **Capabilities**: Search, filter, and extract structured data from ArXiv papers

### Neo4j MCP Server
- **Repository**: https://github.com/neo4j-contrib/mcp-neo4j/tree/main/servers/mcp-neo4j-cypher
- **Purpose**: Executes Cypher queries and manages graph database operations
- **Capabilities**: CRUD operations, complex graph queries, and relationship management

### PDF Reader MCP Server
- **Repository**: https://github.com/sylphxltd/pdf-reader-mcp
- **Purpose**: Extracts text and metadata from PDF documents
- **Capabilities**: Text extraction, author parsing, and reference extraction

## 📈 Data Model

```
(Author)-[:AUTHORED]->(Paper)
(Author)-[:AFFILIATED_WITH]->(Affiliation)
(Paper)-[:CITES]->(Paper)
(Paper)-[:BELONGS_TO]->(Category)
(Author)-[:COLLABORATES_WITH]->(Author)
```

### Node Types
- **Author**: Researchers and paper authors
- **Paper**: ArXiv papers with metadata
- **Affiliation**: Universities, research institutions, companies
- **Category**: ArXiv subject categories

### Relationship Types
- **AUTHORED**: Author wrote the paper
- **AFFILIATED_WITH**: Author's institutional affiliation
- **CITES**: Paper references another paper
- **BELONGS_TO**: Paper belongs to a category
- **COLLABORATES_WITH**: Derived relationship between co-authors

## 🎨 Visualization Examples

The system generates interactive visualizations showing:
- Author collaboration networks
- Institutional research clusters
- Paper citation networks
- Cross-institutional collaborations
- Temporal research trends

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Original team (@Claudio9701 @neohack22 @maxscience @dalila3244)
- [ArXiv](https://arxiv.org/) for providing open access to research papers
- [Neo4j](https://neo4j.com/) for the powerful graph database
- [Anthropic](https://www.anthropic.com/) for Claude AI
- MCP server contributors for enabling seamless integrations

---

**Built with ❤️ by the research community, for the research community**
