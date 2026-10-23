# 2. Literature Review

> **Note to the author:** the sources below are well-known works in the field, cited from memory. Before submission, verify every citation (volume/pages/DOI) in Google Scholar, and add 5–10 recent papers (2023–2026) on your chosen focus (e.g. explainability, cold start, fairness) so the review reflects current work. Citation keys like [Koren2009] map to section 2.9.

## 2.1 What is a recommender system?

A recommender system predicts which items from a large catalogue a particular user is likely to value, and presents them as a ranked list [Ricci2022]. In the movie domain the data are typically *users × items* interactions: explicit (star ratings) or implicit (watched, clicked, added to watchlist). Two formulations dominate:

* **Rating prediction** — estimate the rating a user would give (evaluated with RMSE/MAE). Popularised by the Netflix Prize [Bennett2007].
* **Top-N recommendation** — produce a ranked list of N items (evaluated with Precision/Recall/NDCG). Offline studies show these two goals can lead to different conclusions, and top-N ranking is closer to the real product task [Cremonesi2010], [Herlocker2004].

CineMatch targets **top-N recommendation** with explicit feedback converted to a ranking problem.

## 2.2 Content-based filtering

Content-based methods recommend items whose attributes are similar to items the user liked: items and user profiles are vectors over features such as genres, cast, keywords or text (TF-IDF, embeddings), and ranking uses cosine similarity [Pazzani2007], [Lops2011].

* ✅ Works for new items (no interactions needed), transparent explanations ("same genre/keywords").
* ❌ Over-specialisation ("filter bubble"), requires good metadata, cannot capture taste beyond the features.

## 2.3 Collaborative filtering (CF)

CF uses only the interaction matrix: users with similar histories like similar things.

* **User-based CF** — GroupLens [Resnick1994] pioneered neighbourhood methods; simple but scales poorly with many users.
* **Item-based CF** — precompute item-item similarities offline; faster and more stable [Sarwar2001], [Linden2003] (Amazon's production approach). Good fit for explanations ("because you liked X").
* **Matrix factorization (MF)** — learns latent factors for users and items; a core part of the winning Netflix Prize solutions [Koren2009]. For implicit feedback, weighted ALS [Hu2008] and Bayesian Personalized Ranking (BPR) [Rendle2009] optimise ranking directly.
* **Neural CF** — NCF [He2017] replaced dot products with MLPs, but a careful re-evaluation showed that a well-tuned dot-product MF is highly competitive [Rendle2020], and reproducibility studies found many neural results hard to replicate or not better than tuned simple baselines [Dacrema2019]. This motivates CineMatch's decision to **start with strong simple baselines** and treat neural models as optional experiments.
* **Sequential / session models** — GRU4Rec [Hidasi2016] and self-attention SASRec [Kang2018] model the order of interactions; valuable when timestamps/sessions are rich, a stretch goal here.
* **Industrial deep models** — two-stage candidate generation + ranking at YouTube scale [Covington2016]; Netflix describes a portfolio of algorithms and personalised page layout [GomezUribe2015]. The lesson for CineMatch: blend multiple signals and personalise the *presentation*, not only the ranking.

## 2.4 Hybrid recommenders

Hybrids combine approaches to offset weaknesses — weighted, switching, mixed, feature-combination, cascade, meta-level [Burke2002]. CineMatch uses a **weighted hybrid with switching for cold start**: for users with few ratings the content-based/popularity weight is higher; as interactions grow, CF/MF weights increase. Weights are tuned on a validation split.

## 2.5 Cold start and sparsity

New users and new items lack interactions [Schein2002]. Typical remedies: onboarding questionnaires (genres, rating a few popular-but-divisive films), content-based fallbacks, popularity priors, and demographic/contextual data. CineMatch uses genre selection + rating ≥ 5 items, then content-based and popularity-weighted scoring until enough ratings exist.

## 2.6 Evaluation of recommender systems

* **Offline accuracy**: Precision@K, Recall@K, MAP, NDCG [Jarvelin2002]; careful data splitting (per-user holdout or temporal split) and sampling choices matter [Cremonesi2010], [Herlocker2004].
* **Beyond accuracy**: coverage, novelty, serendipity [Ge2010]; diversity of lists [Ziegler2005].
* **User-centric**: perceived accuracy, novelty, satisfaction and trust via questionnaires such as ResQue [Pu2011]; usability via SUS [Brooke1996].
* **Bias**: popularity, exposure and selection biases distort both training data and offline metrics [Chen2023].

CineMatch combines all three levels (offline accuracy, beyond-accuracy, user study) — see doc 07.

## 2.7 Explainability and user control

Explanations improve transparency, trust, scrutability and satisfaction when well designed [Tintarev2007]. Item-based and content-based methods yield natural explanations ("because you liked X", "shares genres Y, Z"). CineMatch attaches a reason to each recommendation and offers *not interested* feedback as a scrutability mechanism.

## 2.8 Datasets, ethics and privacy

* **MovieLens** is the standard benchmark for movie recommendation research and comes in several sizes (100k to 25M ratings) [Harper2015]. Limitations: self-selected raters, popularity skew, ratings mostly from the US/Western audience.
* **Privacy**: sparse rating data can re-identify individuals even when anonymised — shown on the Netflix Prize data [Narayanan2008]. CineMatch minimises collected data, hashes passwords, supports deletion/export and avoids third-party trackers.
* **Purpose of recommendation**: recommenders should serve user (and not only business) goals; value-aware design matters [Jannach2016].

## 2.9 Gaps and the contribution of this project

| Gap observed | CineMatch response |
|---|---|
| Strong personalisation exists only inside closed commercial services | Open, platform-independent web platform with transparent logic |
| Research code (MovieLens experiments) rarely ships as a usable product with explanations | End-to-end platform: data → model → API → UI |
| Many papers compare complex models without tuned simple baselines [Dacrema2019] | Systematic comparison of baselines, CF, MF and hybrid under one protocol |
| Cold start is often handled ad hoc | Explicit onboarding + switching hybrid, evaluated separately for new vs. active users |
| Accuracy-only evaluation | Accuracy + coverage/novelty/diversity + user study |

## 2.10 Summary of design decisions informed by the literature

1. Frame the task as **top-N ranking**; report NDCG@10, Recall@10 as primary metrics [Cremonesi2010].
2. Use **item-based CF and ALS/BPR MF** as main CF components [Sarwar2001], [Hu2008], [Rendle2009]; compare with tuned baselines before any neural model [Rendle2020], [Dacrema2019].
3. Use a **weighted hybrid with cold-start switching** [Burke2002], [Schein2002].
4. Provide **explanations and negative feedback** [Tintarev2007].
5. Evaluate beyond accuracy and with users [Ge2010], [Pu2011], [Brooke1996].
6. Treat **privacy** as a requirement from the start [Narayanan2008].

## 2.11 References

* **[Bennett2007]** Bennett, J., Lanning, S. (2007). The Netflix Prize. *Proceedings of KDD Cup and Workshop 2007*.
* **[Brooke1996]** Brooke, J. (1996). SUS: a "quick and dirty" usability scale. In *Usability Evaluation in Industry*, Taylor & Francis.
* **[Burke2002]** Burke, R. (2002). Hybrid recommender systems: survey and experiments. *User Modeling and User-Adapted Interaction*, 12(4), 331–370.
* **[Chen2023]** Chen, J., Dong, H., Wang, X., Feng, F., Wang, M., He, X. (2023). Bias and debias in recommender system: a survey and future directions. *ACM Transactions on Information Systems*, 41(3).
* **[Covington2016]** Covington, P., Adams, J., Sargin, E. (2016). Deep neural networks for YouTube recommendations. *RecSys '16*.
* **[Cremonesi2010]** Cremonesi, P., Koren, Y., Turrin, R. (2010). Performance of recommender algorithms on top-N recommendation tasks. *RecSys '10*.
* **[Dacrema2019]** Ferrari Dacrema, M., Cremonesi, P., Jannach, D. (2019). Are we really making much progress? A worrying analysis of recent neural recommendation approaches. *RecSys '19*.
* **[Ge2010]** Ge, M., Delgado-Battenfeld, C., Jannach, D. (2010). Beyond accuracy: evaluating recommender systems by coverage and serendipity. *RecSys '10*.
* **[GomezUribe2015]** Gomez-Uribe, C. A., Hunt, N. (2015). The Netflix recommender system: algorithms, business value, and innovation. *ACM Transactions on Management Information Systems*, 6(4).
* **[Harper2015]** Harper, F. M., Konstan, J. A. (2015). The MovieLens datasets: history and context. *ACM Transactions on Interactive Intelligent Systems*, 5(4).
* **[He2017]** He, X., Liao, L., Zhang, H., Nie, L., Hu, X., Chua, T.-S. (2017). Neural collaborative filtering. *WWW '17*.
* **[Herlocker2004]** Herlocker, J. L., Konstan, J. A., Terveen, L. G., Riedl, J. T. (2004). Evaluating collaborative filtering recommender systems. *ACM Transactions on Information Systems*, 22(1), 5–53.
* **[Hidasi2016]** Hidasi, B., Karatzoglou, A., Baltrunas, L., Tikk, D. (2016). Session-based recommendations with recurrent neural networks. *ICLR 2016*.
* **[Hu2008]** Hu, Y., Koren, Y., Volinsky, C. (2008). Collaborative filtering for implicit feedback datasets. *ICDM 2008*.
* **[Jannach2016]** Jannach, D., Adomavicius, G. (2016). Recommendations with a purpose. *RecSys '16*.
* **[Jarvelin2002]** Järvelin, K., Kekäläinen, J. (2002). Cumulated gain-based evaluation of IR techniques. *ACM Transactions on Information Systems*, 20(4), 422–446.
* **[Kang2018]** Kang, W.-C., McAuley, J. (2018). Self-attentive sequential recommendation. *ICDM 2018*.
* **[Koren2009]** Koren, Y., Bell, R., Volinsky, C. (2009). Matrix factorization techniques for recommender systems. *IEEE Computer*, 42(8), 30–37.
* **[Linden2003]** Linden, G., Smith, B., York, J. (2003). Amazon.com recommendations: item-to-item collaborative filtering. *IEEE Internet Computing*, 7(1), 76–80.
* **[Lops2011]** Lops, P., de Gemmis, M., Semeraro, G. (2011). Content-based recommender systems: state of the art and trends. In *Recommender Systems Handbook*, Springer.
* **[Narayanan2008]** Narayanan, A., Shmatikov, V. (2008). Robust de-anonymization of large sparse datasets. *IEEE Symposium on Security and Privacy*.
* **[Pazzani2007]** Pazzani, M. J., Billsus, D. (2007). Content-based recommendation systems. In *The Adaptive Web*, Springer.
* **[Pu2011]** Pu, P., Chen, L., Hu, R. (2011). A user-centric evaluation framework for recommender systems. *RecSys '11*.
* **[Rendle2009]** Rendle, S., Freudenthaler, C., Gantner, Z., Schmidt-Thieme, L. (2009). BPR: Bayesian personalized ranking from implicit feedback. *UAI 2009*.
* **[Rendle2020]** Rendle, S., Krichene, W., Zhang, L., Anderson, J. (2020). Neural collaborative filtering vs. matrix factorization revisited. *RecSys '20*.
* **[Resnick1994]** Resnick, P., Iacovou, N., Suchak, M., Bergstrom, P., Riedl, J. (1994). GroupLens: an open architecture for collaborative filtering of netnews. *CSCW '94*.
* **[Ricci2022]** Ricci, F., Rokach, L., Shapira, B. (eds.) (2022). *Recommender Systems Handbook*, 3rd ed., Springer.
* **[Sarwar2001]** Sarwar, B., Karypis, G., Konstan, J., Riedl, J. (2001). Item-based collaborative filtering recommendation algorithms. *WWW '01*.
* **[Schein2002]** Schein, A. I., Popescul, A., Ungar, L. H., Pennock, D. M. (2002). Methods and metrics for cold-start recommendations. *SIGIR '02*.
* **[Tintarev2007]** Tintarev, N., Masthoff, J. (2007). A survey of explanations in recommender systems. *ICDE Workshops 2007*.
* **[Ziegler2005]** Ziegler, C.-N., McNee, S. M., Konstan, J. A., Lausen, G. (2005). Improving recommendation lists through topic diversification. *WWW '05*.
