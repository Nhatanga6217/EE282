# Analysis Proposal: Using Public Sequencing Data to Investigate Alternative Polyadenylation and m6A Dynamics During Human Stem Cell-to-Islet Differentiation


**Neha Hatangadi**

## Introduction ##

My research goal is to understand how human stem cells control their mRNAs as they are chemically induced to differentiate into insulin-producing islet cells. In my topic proposal, I asked whether genes change the length of their 3' untranslated regions (3' UTRs) through alternative polyadenylation (APA) during differentiation, and whether these changes relate to m6A, a chemical mark that sits mostly near the 3' end of mRNAs (Ke et al. 2015). As the 3' UTR helps control mRNA stability and translation, APA is a layer of gene regulation that standard expression analyses often miss (Tian and Manley 2017). This proposal lays out how I will answer these questions: the data I will use, the analyses I will run, the figures I will make, and why the plan can be finished by the December 4 deadline. The same pipeline is the core of my rotation project in Dr. Yang's lab, so the work directly relates to my rotation research.

## Goals of the Analysis ##

The analysis has one main goal and one follow-up goal. The main goal is to identify genes whose 3' UTRs become shorter or longer between each stage of differentiation, and to separate these APA changes from simple changes in expression. The follow-up goal is to test whether genes with APA changes are more likely to carry m6A near their 3' ends.

## Dataset and How I Will Acquire It ##

I will use the public dataset from Ma et al. (2022; GEO accession GSE163964). It contains bulk RNA-seq and m6A-seq from human embryonic stem cells differentiated into islet-like organoids, sampled at four stages: hESC, definitive endoderm (DE), pancreatic progenitor (PP), and islet-like organoid (hILO). Each stage has two biological replicates, sequenced as 150 bp paired-end reads. For the APA analysis I will use only the input samples (the RNA-seq libraries made without the m6A antibody), because their mRNA was enriched with oligo-dT and captures the 3' ends that APA tools need. For the m6A analysis I will add the matching antibody (IP) samples.

To acquire the data, I will find the SRA run accessions through GEO and download the raw FASTQ files directly onto the HPC3 cluster with the SRA Toolkit (`prefetch` and `fasterq-dump`). I will download the human reference genome (GRCh38) and a gene annotation from Gencode. Raw data will live in my repository's `data/raw` folder, while all scripts will be saved in `code/scripts` so every step can be rerun.

## Analyses ##

**Step 1: Processing and quality control.** I will download files with fasterqdump, trim adapters and low-quality bases with Trimgalore, and align reads to GRCh38 with STAR. I will then sort and index the alignments with samtools and confirm that replicates from the same stage cluster together.

**Step 2: APA analysis (main goal).** I will run DaPars2 (Feng et al. 2018) on the aligned reads with a 3' UTR annotation. For each gene, DaPars2 reports a PDUI value, the fraction of transcripts that use the longer 3' UTR. I will compare each stage to the next (hESC to DE, DE to PP, and PP to islet-like) and call a gene's 3' UTR significantly changed if the PDUI changes by at least 0.1 and a padj less than 0.05.

**Step 3: Expression analysis.** In parallel, I will count reads per gene with STAR and use DESeq2 (Love et al. 2014) in R to find genes whose overall expression changes between the same stages, using adjusted p-values. Comparing these results to the APA results will show whether 3' UTR changes happen independently of expression changes. I will also track the expression of known polyadenylation factors (such as NUDT21, CPSF6, and CSTF2) and cell cycle genes (such as MKI67, PCNA, TOP2A), which could explain any global shifts.

**Step 4: m6A comparison (stretch goal).** I will use RADAR (Zhang et al. 2019) to compare each IP sample to its input and identify m6A sites that change between stages, then use bedtools to test whether genes with APA changes have m6A in their 3' UTRs more often than genes without APA changes.

## Planned Visualizations ##

All figures will be made in R with ggplot2:

1. **PCA plot** of all samples, to confirm that replicates group by stage.
2. **Violin plots of PDUI** at each stage, to show whether 3' UTRs lengthen or shorten overall as cells differentiate.
3. **Bar chart** of the number of genes with lengthened versus shortened 3' UTRs at each stage transition.
4. **Scatter plot** of PDUI change against expression fold change, to separate APA from expression changes.
5. **Heatmap** of PDUI values for the significant APA genes across all four stages.
6. **Line plot** of polyadenylation and cell cycle gene expression across stages
7. **Bar chart** (stretch goal) comparing the fraction of APA and non-APA genes with 3' UTR m6A peaks.

## Feasibility ##

This plan is realistic for several reasons. First, the dataset is small: eight input libraries for the main analysis, which HPC3 can process in a few days using SLURM job arrays. Second, I have already practiced this exact pipeline (trimming, STAR alignment, and DaPars2) on a separate dataset in my rotation lab, and I have begun processing the Ma input samples, so the riskiest technical steps are already working. Third, every tool is free and widely used, and lab members are available for help. Finally, the goals are tiered: the APA and expression results alone make a complete project, and the m6A comparison is added only if time allows. My timeline is to finish alignment by early November, complete the APA and expression analyses by mid-November, run the m6A comparison by Thanksgiving, and finalize figures and the write-up by December 4. The main limitation is that two replicates per stage give limited statistical power, so I will present the results as exploratory and focus on large, consistent changes.

## Conclusion ##

This analysis plan turns my research question into a concrete, finishable project. Using one public dataset, standard tools, and a pipeline I have already tested, I will map how 3' UTR usage changes as human stem cells become islet cells, and, if time allows, test whether these changes are linked to m6A.The project also uses nearly every part of EE282, from Unix and cluster computing, to installing tools such as DaPars2 and RADAR from GitHub, to sequence and alignment file formats, R, and ggplot2. Most importantly, the result will be a documented, reusable pipeline that I can carry directly into my rotation research.

## References ##

Feng X, Li L, Wagner EJ, Li W. 2018. TC3A: The Cancer 3′ UTR Atlas. *Nucleic Acids Res* **46**: D1027–D1030. doi:10.1093/nar/gkx892

Ke S, Alemu EA, Mertens C, Gantman EC, Fak JJ, Mele A, Haripal B, Zucker-Scharff I, Moore MJ, Park CY, et al. 2015. A majority of m6A residues are in the last exons, allowing the potential for 3′ UTR regulation. *Genes Dev* **29**: 2037–2053. doi:10.1101/gad.269415.115

Love MI, Huber W, Anders S. 2014. Moderated estimation of fold change and dispersion for RNA-seq data with DESeq2. *Genome Biol* **15**: 550. doi:10.1186/s13059-014-0550-8

Ma X, Cao J, Zhou Z, Lu Y, Li Q, Jin Y, Chen G, Wang W, Ge W, Chen X, et al. 2022. N6-methyladenosine modification-mediated mRNA metabolism is essential for human pancreatic lineage specification and islet organogenesis. *Nat Commun* **13**: 4148. doi:10.1038/s41467-022-31698-2

Tian B, Manley JL. 2017. Alternative polyadenylation of mRNA precursors. *Nat Rev Mol Cell Biol* **18**: 18–30. doi:10.1038/nrm.2016.116

Zhang Z, Zhan Q, Eckert M, Zhu A, Chryplewicz A, De Jesus DF, Ren D, Kulkarni RN, Lengyel E, He C, et al. 2019. RADAR: differential analysis of MeRIP-seq data with a random effect model. *Genome Biol* **20**: 294. doi:10.1186/s13059-019-1915-9

