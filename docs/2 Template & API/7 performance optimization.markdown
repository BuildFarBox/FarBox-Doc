Title: Performance Optimization
URL: performance-optimization

## Optimizations in Data Access Calls

### Data Layers

There are three data layers in FarBox: `Cache Layer`, `Core Database Layer`, and `Fulltext Search Layer`. The performance is degressive in the sequence that mentioned above.

Data API calls is mainly from `Core Database Layer`. But when fulltext searching, hits `Fulltext Search Layer` first than `Core Database Layer`.

### Cache

#### Page Cache

Every rendered page will be cached in the server for 24 hours.

The cache key of a page is computed by `URL + last updated date of site + language of web browser`.

#### Partial Cache

If `macro` is used in a template file, and the name of macro function starts with `cache_`, then the partial cache will be used. 

The partial cache key is computed by `macro function name + parameters + last updated date of site + language of web browser`.

#### Super Partial Cache

> A question comes, if a page is stored in the cache, will the visits of a post is stored too?

To handle this situation, in FarBox system, we have another kind of cache, named `Super Partial Cache`.


### Rendering Time

The rendering time of every page can't exceed 2.5 seconds (a common page usually less than 0.2 seconds; a cached page usually less than 0.02 seconds in our system, and most time less than 0.005 seconds), otherwise raising timeout error.

FarBox will guess and estimate other pages' rendering time, if rendering lasts a long time frequently, the max acceptable rendering time will be less than 2.5 seconds.


## Optimizations in Template and Pages

- Do not load too many resources (like css/js) in one page, otherwise it can lead to blocking a visitor's IP by server if the visitor reresh several times in a short time.
- In your css file, try to reduce using images.
- Try to use SASS/SCSS/LESS instead of traditional CSS.