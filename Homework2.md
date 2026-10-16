# Homework 2

## Problem 1: Making, navigating and managing Directories

### Background
You are analyzing RNA-seq results and want to organize your gene lists into folders. In this problem, you will create a small folder structure, move around inside it, and clean up a folder you no longer need
.

### Instructions

Write BASH commands for each step below, one command per step. Start from your home directory. Directory names are case-sensitive, so type directory names as shown:

1. Create a directory called `Gene_list`
2. Move into `Gene_list`
3. Inside `Gene_list`, create two subdirectories:`upregulated` and `downregulated`
4. Move into the `downregulated` directory
5. Without typing the full path, move back into `Gene_list`
6. Inside `Gene_list`, create a new directory called `downregulated_adj`
7. Delete the `downregulated` directory

### Answer Key
````bash

mkdir Gene_list                      # Step 1: create Gene_list
cd Gene_list                         # Step 2: move into Gene_list
mkdir upregulated downregulated      # Step 3: create both subdirectories
cd downregulated                     # Step 4: move into downregulated
cd ../                               # Step 5: go up one level, back to Gene_list
mkdir downregulated_adj              # Step 6: create the new directory
rmdir downregulated                  # Step 7: remove the empty directory
````
## Problem 2: Accessing Columns in R by Name

### Background
You have loaded your list of upregulated genes into R. The same data is stored two ways: as a data frame (`genes_df`) and as a matrix (`genes_mat`). Run the setup code below to create both.

````
genes_df <- data.frame(
  gene = c("INS", "GCG", "SST"),
  fold_change = c(5.1, 3.4, 2.2),
  p_value = c(0.001, 0.003, 0.010)
)
genes_mat <- as.matrix(genes_df[, c("fold_change", "p_value")])
```

When printed, the data frame `genes_df` should look like this:

```
  gene fold_change p_value
1  INS         5.1   0.001
2  GCG         3.4   0.003
3  SST         2.2   0.010
```

And the matrix `genes_mat` should look like this:

```
     fold_change p_value
[1,]         5.1   0.001
[2,]         3.4   0.003
[3,]         2.2   0.010
```

### Instructions
Each of the following commands tries to pull out the `fold_change` column:

```r
genes_mat[, "fold_change"]
genes_df[, "fold_change"]
genes_df["fold_change"]
genes_df$fold_change
genes_df[["fold_change"]]
```

1. For each command, state whether it returns a **vector** (just the values) or a **data frame** (a table with one column).
2. One of these commands returns something different from the other four. Which one is it, and why?

### Answer Key

- `genes_mat[, "fold_change"]` returns a **vector**
- `genes_df[, "fold_change"]` returns a **vector**
- `genes_df["fold_change"]` returns a **data frame**
- `genes_df$fold_change` returns a **vector**
- `genes_df[["fold_change"]]` returns a **vector**

The vectors all print the same values on one line:

```
[1] 5.1 3.4 2.2
```

The data frame prints as a one-column table:

```
  fold_change
1         5.1
2         3.4
3         2.2
```

`genes_df["fold_change"]` is the odd one out. It returns a data frame with one column. The other four commands return just the values, as a vector.

Single brackets keep the data frame structure, while `$`, `[[ ]]`, and `[row, column]` return the column's values alone.

## Problem 3: Sharing a Directory and File with a Labmate

### Background
After Problem 1, create an empty file for your gene list by running `touch ~/Gene_list/upregulated/upregulated_genes.csv`. Your home directory now looks like this:

```
~ (home)
└── Gene_list
    ├── upregulated
    │   └── upregulated_genes.csv
    └── downregulated_adj
```

A labmate in your Unix group wants to look at your upregulated gene list. Assume that right now, only you (the owner) have any permissions on these directories and the file.

### Instructions
1. Using `chmod` with letters (`u`, `g`, `o`, `+`, `-`, `r`, `w`, `x`), not numbers, write the commands that let your labmate:
   - list the contents of the `upregulated` directory, and
   - view `upregulated_genes.csv`,

   **without** letting them list the contents of your home directory or of `Gene_list`.
2. After your commands, would your labmate be able to run each of the following? Explain why or why not.
- `ls ~yourusername/Gene_list`
- `cat ~yourusername/Gene_list/upregulated/upregulated_genes.csv`

(Here `~yourusername` means your home directory; replace `yourusername` with your actual username.)

### Answer Key

```bash
chmod g+x ~                                               # home: pass through only
chmod g+x ~/Gene_list                                     # Gene_list: pass through only
chmod g+rx ~/Gene_list/upregulated                        # upregulated: enter and list
chmod g+r ~/Gene_list/upregulated/upregulated_genes.csv   # file: read
```

For a directory, `x` means you can pass through it, and `r` means you can list what is inside. To reach a file, every directory on the path to it must have `x`. To view the file itself, it must have `r`.

Home and `Gene_list` get only `x`, so the labmate can walk through them but cannot see what else is there (such as `downregulated_adj`). The `upregulated` directory gets `r` and `x`, so the labmate can enter it and list its contents. The file gets `r`, so the labmate can view it.

- `ls ~/Gene_list`: **No.** `Gene_list` has `x` but not `r` for the group, so listing it gives "Permission denied."
- `cat ~/Gene_list/upregulated/upregulated_genes.csv`: **Yes.** Every directory on the path has `x`, and the file has `r`.




