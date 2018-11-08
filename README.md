
## ClusterR
<br>

The ClusterR package consists of Gaussian mixture models, k-means, mini-batch-kmeans, k-medoids and affinity propagation clustering algorithms with the option to plot, validate, predict (new data) and find the optimal number of clusters. The package takes advantage of 'RcppArmadillo' to speed up the computationally intensive parts of the functions. More details on the functionality of ClusterR can be found in the package Documentation and Vignette.
<br><br>

**UPDATE 16-08-2018**


As of version 1.1.4 the *ClusterR* package allows R package maintainers to perform **linking between packages at a C++ code (Rcpp) level**. This means that the Rcpp functions of the *ClusterR* package can be called in the C++ files of another package. In the next lines I'll give detailed explanations on how this can be done:

<br>

Assumming that an R package ('PackageA') calls one of the *ClusterR* Rcpp functions. Then the maintainer of 'PackageA' has to :

<br>

* **1st.** install the *ClusterR* package to take advantage of the new functionality either from CRAN using,

<br>


```R

install.packages("ClusterR")
 

```

<br>

or download the latest version from Github using the *devtools* package,

<br>

```R

devtools::install_github('mlampros/ClusterR')
 

```

<br>

* **2nd.** update the **DESCRIPTION** file of 'PackageA' and especially the *LinkingTo* field by adding the *ClusterR* package (besides any other packages),

<br>

```R

LinkingTo: ClusterR

```

<br>

* **3rd.** open a **new C++ file** (for instance in Rstudio) and at the top of the file add the following 'headers', 'depends' and 'plugins',

<br>

```R

# include <RcppArmadillo.h>
# include <ClusterRHeader.h>
# include <affinity_propagation.h>
// [[Rcpp::depends("RcppArmadillo")]]
// [[Rcpp::depends(ClusterR)]]
// [[Rcpp::plugins(cpp11)]]


```
<br>

The available functions can be found in the following files: **inst/include/ClusterRHeader.h** and **inst/include/affinity_propagation.h**

<br>

A *complete minimal example* would be :

<br>

```R
# include <RcppArmadillo.h>
# include <ClusterRHeader.h>
# include <affinity_propagation.h>
// [[Rcpp::depends("RcppArmadillo")]]
// [[Rcpp::depends(ClusterR)]]
// [[Rcpp::plugins(cpp11)]]


using namespace clustR;


// [[Rcpp::export]]
Rcpp::List mini_batch_kmeans(arma::mat& data, int clusters, int batch_size, int max_iters, int num_init = 1, 

                            double init_fraction = 1.0, std::string initializer = "kmeans++",
                            
                            int early_stop_iter = 10, bool verbose = false, 
                            
                            Rcpp::Nullable<Rcpp::NumericMatrix> CENTROIDS = R_NilValue, 
                            
                            double tol = 1e-4, double tol_optimal_init = 0.5, int seed = 1) {

  ClustHeader clust_header;

  return clust_header.mini_batch_kmeans(data, clusters, batch_size, max_iters, num_init, init_fraction, 
  
                                        initializer, early_stop_iter, verbose, CENTROIDS, tol, 
                                        
                                        tol_optimal_init, seed);
}


```

<br>

Then, by opening an R file a user can call the *mini_batch_kmeans* function using,

<br>

```R

Rcpp::sourceCpp('example.cpp')              # assuming that the previous Rcpp code is included in 'example.cpp' 
             
set.seed(1)
dat = matrix(runif(100000), nrow = 1000, ncol = 100)

mbkm = mini_batch_kmeans(dat, clusters = 3, batch_size = 50, max_iters = 100, num_init = 2, 

                         init_fraction = 1.0, initializer = "kmeans++", early_stop_iter = 10, 
                         
                         verbose = T, CENTROIDS = NULL, tol = 1e-4, tol_optimal_init = 0.5, seed = 1)
                         
str(mbkm)


```

<br>


Use the following link to report bugs/issues,
<br><br>

[https://github.com/mlampros/ClusterR/issues](https://github.com/mlampros/ClusterR/issues)

<br>
