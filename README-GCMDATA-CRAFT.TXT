From the yard,abazeed paper which had 533 cell lines:
https://www.nature.com/articles/ncomms11428

and from various other CCLE papers,
I cleaned and consolidated to produce the following dataset.


[Note there are only 498 cell lines because I could not get copy number, gene expression, and
mutation data for all 533 cell lines.]


load gcmData.mat

gcmData = 

  struct with fields:

              masterccleids: [1×498 double]
       geneexpressionvalues: [18543×498 double]
    geneExpressionEntrezIds: [18543×1 double]
           copynumbervalues: [23174×498 double]
        copyNumberEntrezIds: [23174×1 double]
             mutationvalues: [3235×498 double]
          geneMutationNames: {3235×1 cell}
                        auc: [1×498 double]
                       site: {1×498 cell}
                  histology: {1×498 cell}
               subhistology: {1×498 cell}


masterccleids are the cell line IDs used by the CCLE project, and we use them here too.

all matrices (geneexpressionvalues, copynumbervalues, and mutationvalues) are stackable
in the sense that their columns (the cell lines) are all the same and in the same order,
which is the order given in masterccleids. So the first column is data for the cell line
with master ccle id = 3. More info on this cell line is available in the fields
  auc (which gives the radiation sensitivity, which is the value we want to predict)
  site (where the cancer originates from, e.g. liver, breast, pancreas,...)
  histology (e.g. carcinoma, glioma, etc)
  subhistology


for naive machine learning with all the data, one could form as the x matrix

x = [gcmData.geneexpressionvalues;gcmData.copynumbervalues;gcmData.mutationvalues];

which would yield a matrix with 44,952 features and 498 cell lines.

and the y values to predict are

y = gcmData.auc;


For prior knowledge informed learning, where we manually select gene sets, etc, we need the
gene names and the specific mutation names too. These are a little trickier.

For copy number and gene expression (which both come from the same paper Rees, Matthew G.,
et al. "Correlating chemical sensitivity and basal gene expression reveals mechanism of
action." https://www.ncbi.nlm.nih.gov/pubmed/26656090) we have EntrezIds. For the mutations,
which come from the paper https://www.ncbi.nlm.nih.gov/pubmed/26482930
they use gene names, and they drill down to particular gene mutations as well. So I just stored
the names they used.

If you then have a list of genes, by name, for the copy number and gene expression, you'll need to
translate them in entrez ids. this python code https://github.com/lwgray/pyEntrezId
 for example:
 
#Convert Gene Symbol to Entrez Gene Id

from PyEntrezId import Conversion

# HGNCID can be just the number or 'HGNC:9425'
Symbol = 'CDK2'
# include your email address
Id = Conversion('dummyemail@dummybunny.info')
EntrezId = Id.convert_symbol_to_entrezid(Symbol)
# Returns a dictionary containing Symbol and Entrez Id
print EntrezID



There are lots of other gene name conversion tools out there too.



Note the original data sets which I got these from are here (this is just for reference,
the hope is you don't need to go back to these original sources).

mutations, seashore paper: zip file here:
ftp://caftpd.nci.nih.gov/pub/OCG-DCC/CTD2/Broad/CTRPv2.2_2015_pub_CancerDisc_5_1210/

copy number and gene expression
ftp://caftpd.nci.nih.gov/pub/OCG-DCC/CTD2/Broad/CTRPv2.1_2016_pub_NatChemBiol_12_109/

auc etc data from the yard paper 
https://www.nature.com/articles/ncomms11428
supplementary data section:
https://images.nature.com/original/nature-assets/ncomms/2016/160425/ncomms11428/extref/ncomms11428-s2.xlsx



And finally there are other mutation, rna-seq etc calls available for lots or all of these cell lines.
These can be found for example here: https://portals.broadinstitute.org/ccle

Happy to provide the matlab code I wrote to produce these data, but for simplicity I'll not ship it for now].

