# *DMLS*: Drosophila Modular-transcription-regulation Literature Screener 


### Related paper:

Tzu-Hsien Yang\*, Sheng-Hang Wu†, Fang-Yuan Zhang†, Hsiu-Chun Tsai†, Ya-Chiao Yang†, Wei-Sheng Wu\*, An automated pipeline to extract the Drosophila modular transcription regulators and targets from massive literature articles, (Under Review)

### Ackowledgement
The software interface is designed following our previous work <a href="https://github.com/cobisLab/YTLR/">YTLR</a>. Therefore, we adopted similar instructions in this README file.  


## Prepare the Environment

Suggested running environments: Linux Ubuntu 16.04.6, Python 3.8.5

We recommend using the conda package to create a new environment for DMLS to automatically include the required python packages. 

Here is an example: 

1. Install the Conda package for your system. The installation of the package can be found <a href="https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html">here</a>. 

2. Create the DMLS Conda environment. This may take a while, depending on the network status.

```
conda create -n "DMLS" python=3.8.5
```

3. Activate your DMLS Conda environment. 

```
conda activate DMLS
```

4. If you are using APPLE MAC systems, please install wget first:

```
brew install wget
``` 

If you are using Microsoft Windows systems, please install wget first:

```
conda install -c menpo wget
``` 

5. If you want to leave the DMLS Conda envrionment, please type:

```
conda deactivate DMLS
```

## Steps to Use DMLS

1. Download the DMLS codes. Please skip this if you have already performed this step before.

```
wget https://cobis-fs.bme.ncku.edu.tw/DMLS/DMLS.tar.gz
```

2. Unzip the file.

```
tar -zxvf DMLS.tar.gz
```

3. Change the working directory.

```
cd DMLS/
```

4. Download the model from the following link and unzip the file. Please skip it if you have done this step.

```
cd main/

wget https://cobis-fs.bme.ncku.edu.tw/DMLS/model.tar.gz

tar zxvf model.tar.gz

cd ..
```

5. If this is the first time you use DMLS, run the following command to install the necessary packages. 

```
pip install -r requirements.txt

python -m spacy download en_core_web_trf 3.2.0
```

If the second command fails, try below:

```
pip install https://github.com/explosion/spacy-models/releases/download/en_core_web_trf-3.2.0/en_core_web_trf-3.2.0.tar.gz
```

Notice that if you have GPU support in your system, you can activate the GPU support in DMLS by the following command:

```
conda install pytorch torchvision torchaudio cudatoolkit=<version> -c pytorch
```

Remember to specify the current version of your cuda GPU support. 


7. **!!ALERT!!** Because the PMC FTP service will be updated daily, we recommend executing the following command before using DMLS functions.
 
``` python update_PMC_xml.py```

8. Prepare the inputs for DMLS. In DMLS, users can input a list of abstracts downloaded using the PubMed download function. 
    >***HOW TO GET ABSTRACTS FROM PubMed***
    >>1. Search the PubMed website for specific keywords to obtain the lists of articles. 
    >>2. Select the abstracts of interest or select all abstracts in the page. 
    >>3. Click the ***save*** button.
    >>4. Choose the ***Abstract(text)***.
    >>5. The .txt file containing all the abstracts will be downloaded.
    >>![](https://imgur.com/LVznF0x)

9. (Optional) Users can specify the full-texts of the selected abstracts by themselves. However, this is an optional step. DMLS can help retrieve the available PMC full texts using the PMC FTP service (Detail can be found <a href ="https://www.ncbi.nlm.nih.gov/pmc/tools/ftp/">here</a>). 

    >***HOW TO GET FULL TEXT FROM PMC***
    >>1. Browse the PubMed website(https://www.ncbi.nlm.nih.gov/pmc/) and choose the paper you want.
    >>2. You can get the full content of the website by using the right mouse button and clicking "save as a new file" to save it as an HTML file. 

10. Put the abstract .txt file and the full-text .html files in the same input folder.  

   We have provided an example input folder named "sample_input."
      
   **SAMPLE INPUT**
   
    ```
    sample_input/ - abstracts.txt
                  - PMC6767705.html
                  - abstract-24945799.txt
                  - abstract-28126841.txt
                  - abstract-28414802.txt
                  - abstract-28760811.txt

    .txt --> abstracts downloaded from PubMed (only abstract)
    .html --> full-texts downloaded from PMC (optional)
    ```

11. Run DMLS and predict the results. The results will be saved in the output folder specified with the -o parameter. The program will download some essential data on the first use.

```
python main.py -i <path to input directory> -check <0/1> -download <0/1> -o <path to the output directory>
```

>**Required arguments:**

>* -i: The input data directory path.

>* -check: Check if the article contains the word "*Drosophila*" or "fly" in the title and abstract (1 --> check, 0 -> do not check, default = 1).

>* -download: Check if the user wants to use the auto full text retrieval script based on the PMC FTP service (1 -> yes, 0 -> no, default = 1).

>* -o: The output directory for the results. 


**Notice!**

***It may take a while (generally, half an hour to several hours, depending on the network bandwidth and the number of articles given) to download the full texts using the PMC ftp services due to its file package structures. We do not suggest using wifi connections for this automated script since the download process may break in wifi transmissions.***

***And due to the restriction of the term usage of the PMC ftp services, only part of the full texts in PMC can be downloaded via this service. Those articles that cannot be downloaded through the PMC ftp services may require an additional PMC website "Save as..." action for each of them. ***

## Example and Output Results

If we use the example inputs with the example command:

```
python main.py -i ./sample_input -check 1 -o CRM_output
```

The results will be generated in the CRM_output folder.

**SAMPLE OUTPUTS**

```
CRM_output/ - CRM-related_articles.tsv
            - download_xml/ - {PMC4063667}.xml
            - CRM_target_gene_summary.tsv
            - CRM_TF_summary.tsv
            - process_w_error/ - non-CRM-related_articles.tsv
                               - pmids_cannot_find_fullText.txt
            - details/ - CRM_target_gene_details.tsv
                       - CRM_TF_details.tsv

```

>**File descriptions:**

>* **CRM-related\_articles.tsv:** Articles that contains CRM evidence.
>* **non-CRM-related\_articles.tsv:** Articles that does not contain CRM evidence.
>* **pmids\_cannot\_find\_fullText.txt:** PMIDs with no available full text. If full texts are available for all articles, the content of the file will be empty.
>* **download_xml/{PMCID}.xml:** the xml files downloaded from the PMC FTP service.
>* **CRM\_target\_gene\_summary.tsv:** the CRM target genes summarized for the CRM articles.
>* **CRM\_TF\_summary.tsv:** the CRM regulatory TFs summarized for the CRM articles.
>* **CRM\_target\_gene\_details.tsv:** Sentence descriptions for the target genes found in the CRM articles.
>* **CRM\_TF\_details.tsv:** Sentence descriptions for the regulatory TFs found in the CRM articles.

### **DMLS Step 1 Outputs**

DMLS Step 1 outputs contain the following: 

```
CRM_output/CRM-related_articles.tsv
process_w_error/CRM_output/pmids_cannot_find_fullText.txt
process_w_error/CRM_output/non-CRM-related_articles.tsv
```

eg. CRM\_ouptut/CRM-related\_articles.tsv

```
PMID	PMCID	Abstract
28126841	PMC5374349	How differential levels of gene expression are controlled in post-mitotic neurons is poorly understood. In the Drosophila retina, expression of the transcription factor Defective Proventriculus (Dve) at distinct cell type-specific levels is required for terminal differentiation of color- and motion-detecting photoreceptors. Here, we find that the activities of two cis-regulatory enhancers are coordinated to drive dve expression in the fly eye. Three transcription factors act on these enhancers to determine cell-type specificity. Negative autoregulation by Dve maintains expression from each enhancer at distinct homeostatic levels. One enhancer acts as an inducible backup ('dark' shadow enhancer) that is normally repressed but becomes active in the absence of the other enhancer. Thus, two enhancers integrate combinatorial transcription factor input, feedback and redundancy to generate cell type-specific levels of dve expression and stable photoreceptor fate. This regulatory logic may represent a general paradigm for how precise levels of gene expression are established and maintained in post-mitotic neurons.
```

>**Column descriptions:**

>* PMID: PubMed ID of the article.
>* PMCID: PubMed Central ID of the article.
>* Abstract: abstract of the paper.

eg. CRM\_ouptut/process\_w\_error/non-CRM-related\_articles.tsv

```
PMID	PMCID	Abstract
29329646		Herbal extracts have been extensively used worldwide for their application on memory improvement, especially among aged and memory-deficit populations. In the present study, the memory loss induced by human Abeta protein over-expression in fruitfly Alzheimer's disease (AD) model was rescued by multiple extracts from Gardenia jasminoides. Three extracts that rich with gardenia yellow, geniposide, and gardenoside components showed distinct rescue effect on memory loss. Further investigation on adding gardenoside into a formula of Ganoderma lucidum, Panax notoginseng and Panax ginseng (GPP) also support its therapeutic effects on memory improvement. Interestingly, the application of GPP and gardenoside did not alter the accumulation of Abeta proteins but suppressed the expression of immune-related genes in the brain. These results revealed the importance and relevancy of anti-inflammation process and the underlying mechanisms on rescuing memory deficits, suggesting the potential therapeutic use of the improved GPP formulation in improving cognition in defined population in the future.
```

>**Column descriptions:**

>* PMID: PubMed ID of the article.
>* PMCID: PubMed Central ID of the article.
>* Abstract: Abstract of the paper.

### **DMLS Step 3 and Step 4 Outputs**

DMLS Step 3 and Step 4 outputs contain the following: 

```
CRM_output/CRM_target_gene_summary.tsv
CRM_output/CRM_TF_summary.tsv
CRM_ouptut/details/CRM_target_gene_details.tsv
CRM_ouptut/details/CRM_TF_details.tsv
CRM_ouptut/download_xml/
```

eg.  CRM\_ouptut/CRM\_target\_gene\_summary.tsv

```
Pmid	FBID	All_synonym
28760811	FBgn0001320	KNIRPS
28126841	FBgn0020307	DEFECTIVE PROVENTRICULUS|DVE
24945799	FBgn0086917	SPOOKIER|SPOK

```

>**Column descriptions:**

>* PMID: PubMed ID of the article.
>* FBID: Fbid of the target gene/regulatory TF alias found in the article.
>* All_synonym: The aliases of the target gene/regulatory TF.

You can find the detailed sentence descriptions in the file named CRM\_{target\_gene/TF}\_details.tsv.

eg. CRM\_ouptut/details/CRM\_target\_gene\_details.tsv

```
PMID	FBID	Synonym	Sentence
28760811	FBgn0001320	KNIRPS	Here, we analyse the mechanisms generating the domain of knirps expression in the presumptive L2 vein of the wing imaginal disc.
```

>**Column descriptions:**

>* PMID: PubMed ID of the article.
>* Fbid: Fbid of target gene/regulatory TF alias found in the sentence.
>* Symonym: Target gene/regulatory TF alias.
>* Sentence: Sentence descriptions for the target gene/regulatory TF found in the article that supports the evidence.
