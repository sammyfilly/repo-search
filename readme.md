## RepoSearch
### Description

RepoSearch is a tool for searching through repositories of source code using natural language queries, based on OpenAI's embeddings.

> **Note**: Using this project requires you to supply your own OpenAI API key (set environment variable `OPENAI_API_KEY=sk-...`)
>
> **Costs**: Cost per query is negligible, almost always less than 1/10th of a penny unless you're writing paragraphs of text. Generating embeddings for the [OpenMW](https://www.gitlab.com) repository costs about $0.20 USD.

### How does it work?
For each file in the repository, a query is sent to the [OpenAI embeddings API](https://platform.openai.com/docs/api-reference/embeddings) to generate an embedding. If a file is too large for a single query, it is split into smaller chunks and each chunk is embedded separately.

The retrieved embeddings are stored in a [HuggingFace Datasets](https://huggingface.co/docs/datasets/index) dataset, which can be used by code outside of this project. Check out [the schema](#dataset-schema) for more information about using the generated dataset.

**TODO**: The embeddings are indexed using [FAISS](https://faiss.ai/), which allows for fast nearest neighbor searches to your queries.

### Example Usage

```bash
$ repo_search generate openmw ~/Developer/openmw/apps --verbose
Loading libraries...
Generating embeddings from local directory /Users/danielperry/Developer/openmw/apps for openmw...
WARNING: Could not read as text file: /Users/danielperry/Developer/openmw/apps/openmw_test_suite/toutf8/data/french-win1252.txt
WARNING: Could not read as text file: /Users/danielperry/Developer/openmw/apps/openmw_test_suite/toutf8/data/russian-win1251.txt
100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 1386/1386 [05:53<00:00,  3.92it/s]

$ ./repoSearch.sh query openmw "NPC navigation code and examples on making an NPC navigate towards a specific destination."

Loading libraries...
Querying embeddings...
100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 1386/1386 [00:00<00:00, 3375.94it/s]
0.7708185244777552: openmw/mwmechanics/aiwander.cpp
0.7648722322559: openmw/mwmechanics/obstacle.cpp
0.7593991785701977: openmw/mwmechanics/aipursue.cpp
0.7570192805465497: openmw/mwmechanics/aiescort.cpp
0.7540042527421098: openmw/mwmechanics/aiescort.hpp
0.7534971127334509: openmw/mwmechanics/aitravel.cpp
0.7531876754013663: openmw/mwmechanics/aiface.cpp
0.7529779124249095: openmw/mwmechanics/aipackage.cpp
0.7527566874958713: openmw/mwworld/actionteleport.cpp
0.7491856749386254: openmw/mwmechanics/pathfinding.cpp
```

### Usage

1. Clone the repository.
2. Open a terminal in the repository directory.
3. `pip install -e .`
4. `repo_search --help`

### Dataset Schema
The generated dataset consists of just two columns.

| Column Name | Description |
| ----------- | ----------- |
| `file_path` | The path to the file that was embedded. Useful for displaying to the user. |
| `embeddings` | An array of embeddings for the file. An empty array indicates an error occurred when generating embeddings for the file. The array may have one or more embeddings within it, depending on the source file length. |