# Using Public Sequencing Data to Investigate Alternative Polyadenylation and m6A Dynamics During Human Stem Cell-to-Islet Differentiation

**Neha Hatangadi**

## Introduction ##
Human pluripotent stem cells (hPSCs) can be directed to differentiate into insulin-producing, beta-like cells through stepwise protocols that mimic how the pancreas forms in the embryo (Pagliuca et al. 2014). These cells are a valuable model of human pancreas development and a promising source of cells for diabetes therapy, but they remain less mature and less functional than adult human beta cells (Augsornworawat et al. 2020). Most studies focus on which genes are turned on or off at each stage, while less is known about how the mRNAs of those genes are processed after they are made. For my final project, I propose to analyze public RNA-seq and m6A-seq data from Ma et al. (2022), which follow hPSCs through islet differentiation, to ask whether genes change their 3' ends through alternative polyadenylation (APA) and whether these changes relate to m6A, the most common internal chemical mark on mRNA. This project draws on the core skills taught in EE282, ties directly to my rotation work in Dr. Yang's lab, and builds on my background in stem cell differentiation.

## Background ##

### Alternative polyadenylation ###
APA is one such layer of mRNA processing. Most human genes have more than one site where the mRNA can be cut and a poly(A) tail added, and choosing a site closer to or farther from the stop codon changes the length of the 3' untranslated region (3' UTR). Because the 3' UTR helps control mRNA stability, translation, and localization, these length changes can alter how much protein a gene makes (Tian and Manley 2017). In many tissues, 3' UTRs become longer as cells differentiate (Ji et al. 2009), and APA and m6A changes have been linked to cell fate decisions in other lineages, such as neuronal differentiation. In the pancreatic lineage, however, these processes have been less explored, especially beyond the early stages of development. Because fetal pancreatic tissue is limited and mouse islets develop differently from human islets, stem cell differentiation offers a valuable human model in which every stage can be sampled.

### m6A and the link to APA ###
m6A adds a closely connected layer to this question: most m6A sites lie in the last exon and the 3' UTR, the same region where APA takes place, and reducing m6A can shift which poly(A) site a transcript uses (Ke et al. 2015). m6A is also important in this system. Ma et al. (2022) showed that m6A-regulated mRNA metabolism is essential for human pancreatic lineage specification, but they did not examine APA. Studying APA and m6A together could therefore reveal regulation that is missed when only expression levels are measured. Before my PhD, I worked in industry differentiating stem cells toward endocrine and beta-like cells, so I know the biology of this system well. What I lack is the computational skill to ask these questions myself, and this project is designed to build exactly those skills.

## Publicly available RNA-Seq Datasets can be utilized for this project ##
To address these questions, I will use data from Ma et al. (2022; GEO accession GSE163964), which include both bulk RNA-seq and m6A-seq at four stages of differentiation: hPSCs, definitive endoderm, pancreatic progenitors, and islet-like organoids. The mRNA for these samples was enriched with oligo-dT, which makes the RNA-seq well suited to APA analysis because this type of library captures the 3' ends of transcripts. Having both measurements from the same differentiations makes this dataset well matched to my question. The main limitation is the low number of replicates, so I will treat my results as exploratory and focus on large, consistent changes.

## Research Questions and Approach ##
### Question 1: Which genes change their 3' UTR length during differentiation? ###
My main question is which genes shift toward shorter or longer 3' UTRs as cells move from the pluripotent state toward islet cells, and whether any of these poly(A) sites are new compared to standard annotations. To answer it, I will download the raw reads from SRA, check read quality, trim the reads, and align them to the human genome (GRCh38). I will then use DaPars2 (Feng et al. 2018), together with a RefSeq-based 3' UTR annotation, to estimate how much each gene uses its shorter versus longer 3' UTR at each stage. In parallel, I will count reads per gene and use DESeq2 (Love et al. 2014) in R to identify genes whose overall expression changes, using adjusted p-values. Comparing these two results will let me separate genes that change their 3' UTR length from genes that simply change how much they are expressed.

### Question 2: Are APA changes linked to m6A? ###
Building on these results, my second question is whether genes with APA changes are more likely to carry m6A sites near their 3' ends. I will compare my APA genes with the m6A peaks measured in the same samples, using bedtools to find overlaps, and test for enrichment in R. I will also examine whether genes that write, read, or remove m6A (such as METTL3, YTHDF2, and FTO) and genes in the core polyadenylation machinery (such as NUDT21 and CPSF6) change in expression across the same stages. Together, these analyses, visualized in R with ggplot2, would offer a first look at whether m6A and APA work together in islet development.

## Feasibility and Timeline ##
This plan is feasible within the quarter. I already have an active account on the HPC3 cluster, where I will run all analyses, and I will track my scripts and notes in my GitHub repository. To catch problems early, I will first run the pipeline on a small subset of samples, such as the earliest and latest stages, before scaling up. I plan to finish read processing and alignment by early November, complete the APA and expression analyses by mid-November, carry out the m6A comparison by the week before Thanksgiving, and produce final figures before the December 4 deadline. The m6A comparison is quick once the APA results are in hand, since it uses m6A peaks from the same study and expression results I will already have from DESeq2. The work also spans nearly every major EE282 topic, from Unix and git to FASTQ, BAM, and BED files, pipelines, and R.

## Conclusions ##
In summary, this project asks a focused question about how human islet cells regulate their mRNAs during development, using public data and standard tools. By mapping APA changes across differentiation and linking them to m6A, it can offer a new view of gene regulation in islet development within a single quarter. Even if the biological signal is modest because of the low number of replicates, the result will be a documented, working pipeline that I can apply directly to my rotation research and to future datasets. For these reasons, I believe this is a strong and practical choice for my EE282 final project.

## References ##

Augsornworawat P, Maxwell KG, Velazco-Cruz L, Millman JR. 2020. Single-cell transcriptome profiling reveals β cell maturation in stem cell-derived islets after transplantation. Cell Rep 32: 108067. doi:10.1016/j.celrep.2020.108067

Feng X, Li L, Wagner EJ, Li W. 2018. TC3A: The Cancer 3′ UTR Atlas. Nucleic Acids Res 46: D1027–D1030. doi:10.1093/nar/gkx892

Ji Z, Lee JY, Pan Z, Jiang B, Tian B. 2009. Progressive lengthening of 3′ untranslated regions of mRNAs by alternative polyadenylation during mouse embryonic development. Proc Natl Acad Sci 106: 7028–7033. doi:10.1073/pnas.0900028106

Ke S, Alemu EA, Mertens C, Gantman EC, Fak JJ, Mele A, Haripal B, Zucker-Scharff I, Moore MJ, Park CY, et al. 2015. A majority of m6A residues are in the last exons, allowing the potential for 3′ UTR regulation. Genes Dev 29: 2037–2053. doi:10.1101/gad.269415.115

Love MI, Huber W, Anders S. 2014. Moderated estimation of fold change and dispersion for RNA-seq data with DESeq2. Genome Biol 15: 550. doi:10.1186/s13059-014-0550-8

Ma X, Cao J, Zhou Z, Lu Y, Li Q, Jin Y, Chen G, Wang W, Ge W, Chen X, et al. 2022. N6-methyladenosine modification-mediated mRNA metabolism is essential for human pancreatic lineage specification and islet organogenesis. Nat Commun 13: 4148. doi:10.1038/s41467-022-31698-2

Pagliuca FW, Millman JR, Gürtler M, Segel M, Van Dervort A, Ryu JH, Peterson QP, Greiner D, Melton DA. 2014. Generation of functional human pancreatic β cells in vitro. Cell 159: 428–439. doi:10.1016/j.cell.2014.09.040

Tian B, Manley JL. 2017. Alternative polyadenylation of mRNA precursors. Nat Rev Mol Cell Biol 18: 18–30. doi:10.1038/nrm.2016.116

