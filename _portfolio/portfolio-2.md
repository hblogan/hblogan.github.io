---
title: "Sentiment Analysis of Steam Reviews Across 11 Models"
excerpt: "Insert description here"
collection: portfolio

---

## Task

As one of my final projects for my Fall 2023 LING 582 class (Advanced Statistical Natural Language Processing), I decided to train a diverse group of models on the same task in order to become familiar with different types of classifiers.

The task I chose was binary sentiment classification of Steam reviews, based on whether the reviewer recommended the product or not. 

## Contributors

All work was done by the author. 

## Approach

The dataset used was derived from the first 10K English-language reviews in this dataset in order to reduce the time needed to train the tranformer models while retaining high performance. Classifiers were all trained on the first 9K of that set and tested on the last 1K. 

The source dataset can be found here: https://www.kaggle.com/datasets/lucaspoo/steam-reviews-international 

Positive reviews greatly outnumber negative reviews, the significance of which I didn't realize until too late. In total, the training set had 7161 positive reviews and 1839 negative reviews (79.6% positive) and the test set had 744 positive reviews and 256 negative reviews (74.4% positive). 

In total, 11 models were trained on this task:

Non-transformer models (scikit-learn): 

- Two **logistic regression** classifiers (one unbalanced, one balanced)

- Two **naïve Bayes** classifiers (one unbalanced, one balanced)

- Two **random forest** classifiers (one unbalanced, one balanced)

Transformer models:
- Encoder models:
    - **BERT** 
    - two **ELECTRA** models (one from a generator base, one from a discriminator base)
- Encoder-Decoder models 
    - **BART**
- Decoder models:
    - **GPT-2** 

## Training

The non-transformer models were all taken from the **sklearn** library, while the transformer models were all taken from huggingface's **transformers** library. 

For the non-transformer models, a TF-IDF vectorizer was used to encode the review texts. For the balanced versions of the three models, class weights were added to the Logistic Regression Classifier and the Random Forest Classifier during initialization, while sample weights were added to the Naïve Bayes Classifier during the fit method. 

[insert code here]

All the transformer models were trained with three epochs. The BERT model and both ELECTRA models both used a batch size of 8, while to prevent Out-of-Memory errors a batch size of 4 was used for the BART and GPT-2 models. The final 1K training samples were taken out of the training set to form a separate validation set.

[insert code here]



## Results

**Accuracy and F1 scores of all models:**

![scores](https://drive.google.com/uc?id=1Iuh2aadh1sEq8CFze8eUITsrZVf80zfw)

All classifiers performed more poorly on identifying negative reviews (class 0) than positive reviews (class 1). This was especially pronounced with the non-transformer models. 

The highest performing model in all categories was the ELECTRA Discriminator model. Perhaps *a priori* we should expect large pre-trained transformer models to outperform non-transformer models, and in a sequence classification task, encoder models to outperform encoder-decoder and decoder models. 

**Correlation matrix of true and predicted labels of all models:**

![correlation matrix](https://drive.google.com/uc?id=1bJkojWX9EuzP465Dpszdp_FV85nsybPk)

The transformer models all share similarly high correlation coefficients with each other. I expected there to be distinct groupings between encoder and (partial) decoder models, but that wasn't born out.

## Reviews

As one might expect, reviews expressing mixed sentiment, whether the reviewer ultimately recommended the game or not, were often miscategorized.

> ID: 18387
>
>The game is decently fun but there is no content what so ever. I wish there was a way to edit my deck and a story mode or something. As things stand it's not worth the money as the only option available is to fight the AI. **[RECOMMENDED]**

<br>

> ID: 17486
>
>Game is very early access. Mostly no sound effects, core gameplay works, ui is very basic and requires micro management to build what you want. \
>Fun but pricy for what is available right now. **[NOT RECOMMENDED]**

Also often miscategorized, though not confusing to humans, were updated reviews, where the bulk of the text, constituting the old review, indicated one sentiment, and a smaller section, the update, indicated another. There's also the possibility with the transformer models that the updated section might be cut off!

Take this positive review:

> ID: 19204
>
> EDIT: The game is finally working fine - no more controller issues and when I exit the game, I'm no longer placed 2 meters below the floor in SteamVR Home! Performance is also improved - so I can finally recommed the game! 
>
> My old review:
> 
> BEWARE - this is alpha software or early access, it's by far not a game ready for full launch. Performance is significantly worse than the demo, probably due to the added AI players - maybe. I'm using an oc'ed RTX 3090, and this game is just as demanding as Reviving Lone Echo 2 with Ultra settings, lol - but unfortunately I'm not kidding. \
> [...] \
> In short, do not buy this less than half-baked cake, but do get the free demo - the free demo is much better than the lackluster "full" version, even if the demo also may cause locked controllers and falling through the SteamVR floor.

Or this negative review:

> ID: 19270
>
> Overall a great Early Access experience. You find yourself alone in a World just with your cool car and some great music, the atmosphere of this game is quite nice. [...] For right now at the current price i think it's really worth it and i hope the development will go really quickly. \
> [...] \
> 3 bucks well spent :) \
> i will maybe update this feedback once i progress more in the game
> 
> Update:
> 
> i refunded the game due to lack of updates and everything. don't waste your money buy something good instead. sad cause this game had potential

If there is a way for the classifiers to recognize certain sections as updates and weigh them more heavily and protect them from truncation (perhaps by training a separate classifier on this text), this could improve performance. As far as I am aware, this convention of updating reviews (without deleting the old review) is not usually found outside game reviews.

There were six negative reviews in total that all classifiers managed to classify as negative. Five of these were very similar: long reviews calling the game an asset flip, with similar phraseology:

> ID: 18008
>
> Lucky Day  is another GameMaker Studio asset flip from serial copy+paste infringers, Garage Games. All these guys do is rip off game templates and projects from the Yoyogames/GameMaker Studio store and try to scam people into paying for someone else's work on Steam.  They have run asset flip scams dozens of times, and this is no exception. It's nothing but a cash grab. [...] 

<br>

> ID: 18746
>
> Dungless 2 is another GameMaker Studio asset flip from serial copy+paste infringers, ImperiumGame. All these guys do is rip off game templates and projects from the Yoyogames/GameMaker Studio store, change the name and a few cosmetic details, and try to scam people into paying for someone else's work on Steam.  They have run asset flip scams dozens of times, and this is no exception. It's nothing but a cash grab. [...]

19255 and 17510 also seem to share a (different) template as well. Maybe gamers found it ironic to pan an asset flip with a low-effort copy-paste of another review, but it seems that if the same templates were found in the training set only among the negatively-labelled subset this may have outweighed the bias to the majority positive class even among the worse-performing classifiers. (Another 24 negative reviews were only miscategorized by the unweighted Naïve Bayes Classifier, many of which were also accusations of the game being an asset flip). 

The only negative review not of this sort that all classifiers managed to get correct was this:

> ID: 19425
>
> Terrible.

## Improvements

The most obvious path for improvement is the imbalanced dataset, which caused all classifiers to have a worse F1 score with class 0 (the minority label) than with class 1. There are three obvious ways to combat this:

1) since the dataset used was only a subset of a larger dataset, one could collect the same number of training samples where the ratio of labels is 50:50

2) undersampling (removing samples from the majority class)/oversampling (duplicating samples from the minority class). In this case (1) is obviously superior, but this would have been necessary if the whole dataset were used.

3) adding class/sample weights. Class weights were added to the balanced Logistic Regression Classifier and Random Forest Classifier (which both have an inbuilt parameter to add class weights based on inverted frequency), and sample weights to the balanced Naïve Bayes Classifier. This improved performance compared to their counterpart unbalanced classifiers (though not significantly for the Random Forest Classifier). 

And outside of the machine learning part, I could have done a much better job of organizing the project. There was a lot of copy-pasting of functions and code that could have been put into a shared file instead (though this is partly because I didn't know at the outset what could be shared and what needed to be tweaked). 

## Code

You can find my code here: https://github.com/uazhlt-ms-program/ling-582-course-project-code-team-8 
