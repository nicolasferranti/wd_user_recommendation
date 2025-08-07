# wd_user_recommendation

This framework addresses the problem of recommending users capable of repairing inconsistencies in Knowledge Graphs (KGs), such as Wikidata. It combines historical repair data with semantic clustering of item descriptions to enable personalized user recommendations for data repair tasks.

## Framework Pipeline

1. **Data Augmentation**: 
    - Starts from a dataset of historical repairs containing revisionId and the repaired ⟨item, property, value⟩ triples.
    - For each revision, retrieves the corresponding username via the Wikidata API
    - For each QID, retrieve a DBpedia abstract or generate a description using Wikidata triples and mixtral:8x7b LLM .

2. **Semantic Mapping**: 
    - Embed each textual abstract into a dense vector space using the all-distilroberta-v1 sentence transformer model.
    - Experiment with PCA and UMAP for dimensionality reduction, and KMeans, Gaussian Mixture Models (GMM), Spectral Clustering, and HDBSCAN to group semantically similar items.

3. **User Recommendation & Evaluation**:
    - **Frequency-Based Ranking**: Rank users by edit frequency in a cluster.
    - **Cosine Similarity Ranking**: Rank users based on how semantically close they are to the query item (QID), using average cosine similarity between embeddings.
    - Evaluate recommendations using **Hits@K** and **Membership Hit Rate**.

## Project Structure

```bash
WD_USER_RECOMMENDATION/  
├── experiments/                    # Some experiment results (metrics and plots)  
│   ├── all/                        # Experiments using the full set (DBpedia abstracts + Wikidata triple-based texts)  
│   ├── only_dbpedia_abstracts/     # Experiments using only DBpedia abstracts  
│   └── only_wikidata_texts/        # Experiments using only Wikidata-generated texts  
│  
├── resources/                      # Datasets and auxiliary files used in the pipeline  
│   ├── data_augmentation/          # Files for textual data enrichment  
│   │   ├── dbpedia_abstracts/  
│   │   └── wikidata_texts/  
│   ├── final_entity_texts.csv      # Merged descriptions (DBpedia abstracts + Wikidata triple-based texts) used for embeddings  
│   ├── sitelinks_en.tsv            # English sitelinks (entity -> URL), used to link DBpedia resources  
│   └── users_one_of.tsv            # Repair logs: list of edits performed by users on “one of” constraint violations  
│  
├── semantic_mapping/               # Embedding mappings for each experiment setup  
│   ├── qid_to_embedding_all.pkl    # Embeddings using full textual data  
│   ├── qid_to_embedding_only_abstracts.pkl # Embeddings using only DBpedia abstracts  
│   └── qid_to_embedding_wikidata_texts.pkl.tar.gz   # Embeddings using only Wikidata triple-based texts 
│  
├── scripts/                        # Jupyter notebooks for each stage of the pipeline    
│   ├── 1-data_augmentation.ipynb   
│   ├── 2-semantic_mapping.ipynb    
│   └── 3-recommendation-and-evaluation.ipynb  
│  
├── .gitattributes  
└── README.md  
```
