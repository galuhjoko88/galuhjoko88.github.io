---
title: Risk in AI part 1 - Classification
date: 2023-12-01 01:00:00 +0700
categories: [risks, machine learning, AI, AI risks, business value ]
tags: [risk management, AI, machine learning, Business AI application]
---


## Machine Learning Application Risks Part 1 ~ Classification

According to stanford university (https://ocro.stanford.edu/enterprise-risk-management-erm/key-definitions/definition-risk), risks are The possibility that the occurrence of an event will  adversely affect the achievement of the organization's objectives.

While, according to HBR (https://hbr.org/2021/01/when-machine-learning-goes-off-the-rails), AI also had risks associated with the business that is "controlled by AI". What is actually this risk ? How we can explain it plainly in layman terms and deliver to management eecutives without creating havocs that jeopardize your projects?

First of all, any of literature will said that risks are probability. Ok, so what ? Probability of what ? Why should I care ? The potential dangers associated with AI often go unnoticed, overshadowed by the prevailing excitement surrounding AI advancements. I had rarely come across articles or discussions within my previous, and current, organizations that delve into the impact of these risks on businesses. This lack of attention indicates a significant gap in perspective, especially among individuals with a purely technical background in data science, who may not readily consider these risks. 

There are a minimum of two approaches to assess risks associated with AI, and the choice depends on the type of Machine Learning employed. For the sake of illustration simplicity, let's focus on a prevalent branch in supervised learning: (i) classification, and (ii) regression. When dealing with a classification challenge, a tool commonly utilized in six sigma and traditional statistics is the well known error matrix, also recognized in the field of Data Science as the confusion matrix.

Error matrix can be described theoretically in https://www.analyticsvidhya.com/blog/2020/04/confusion-matrix-machine-learning/ or you can google it and it will show you a ton of these. However, most of the explanation is too vague and highly technical and sometime many people need more time to grasp pragmatically even if you are statiscian :).

I will re-word some of the concept and show some example below.

![confusion matrix](</assets/img/riskAI/confmatrix.PNG>)

I utilized a case from a Machine Learning competition involving face recognition classification, and the error matrix employed is as follows (Note: The actual sample numbers have been adjusted for simplicity in this article).

![example conf matrix](</assets/img/riskAI/example confmatrix.PNG>)

Assessing the provided matrix, a common query that often arises is, "Can we rely on this prediction method?" The response to this question is contingent upon various factors. To illustrate, let's consider the consequences of incorrect classifications using a well-known face recognition AI. While this AI has numerous applications, I will focus on two specific use cases from facial recognition to highlight the potential risks in AI.

1. Face recognition to classify bad guys
2. Face recognition to check absentism

Referring to these two cases, let's identify and define the common risks that arise from them.

## A.  Identifying bad guys
From the algorithm results, we have each metric defined. As the AI users, we must defined what is the acceptable risks that we are willing to accept. How ? Let's delve into each four of the metrics.

![bad guy conf matrix](</assets/img/riskAI/example bad guy.PNG>)

*Accuracy 80%*, it's decent, meaning it will predict 80% face correctly and 20% wrong. Wait, is that mean that we can conclude bad guys as good guy or good guy as bad guy ? Well, it's both. This metric are the most selling point of any AI product these days and rarely questioned by management. 

Precision 78.6% means that 87.6% of bad guys prediction are correct. Sensitivity 78.6%, means that prediction will correctly define bad guys 78.6% from all actual bad guys. Specifity 83.3% means that good guy will be correctly identified as "good guy" 83.3% of the time. Well this is confusing... What's in it for me as management that use this product ? Actually there is, but this metric are usefull if you understand the whole algorithm used and you already know what you are dealing with :). So basically this metric mostly used by data science guys. 

In practical management terms, my approach typically involves requesting the error matrix and calculating key metrics. This becomes particularly valuable when utilizing external vendors for prediction modeling, as they often possess high technical expertise but may lack a deep understanding of internal business dynamics. Taking the current case as an example, I define my losses arising from wrongly identifying a bad actor as a good one (false negatives, FN). The associated risks include:

a. Financial loss stemming from subsequent criminal activities.
b. Loss of lives due to further criminal actions.

Let's say the "losses in monetary value" are $ 300 billion (considering possibility in losses of lives, this might be inappropriate, but let's use this one only for illustration).

While the risks from wrongly defined good guy as bad guy (false positive, FP), for example, are:
a. image of the presecutor
b. legal suit directed to you
c. reputation of the company
d. public trusts to company, etc.
Let's say the losses from this miss-classification in monetary terms are $20 billion.

If the algorithm correctly identify appropriate person (TN and TP), then there will be no losses and let's say we can gather total benefit (TB) of $60 billion. From this fact, we can measure some risks that must be anticipated by organzation as:
	Total Risk = TR = (FP/Total sample x Loss from FP) + (FN/Total sample x Loss from FN)
	
	Expected Benefit = EB = ((TP + TN)/Total sample) x Total Benefit - TR - Cost development
	
	EB 	= ((110+50)/200x $60 bn - (10/200 x $20 bn) - (30/200 x $300 bn) - TC
		= $48 bn - $1 bn - $45 bn - TC
		= $2 bn - TC 
		~ 3.4% of total benefit retained

Given that the anticipated benefit for this case amounts to only +$2 billion in monetary terms, the question arises: Is this acceptable for your organization? Personally, I hold the view that, regardless of the total development cost (TC), the acceptance of this prediction method is questionable. However, it's important to note that perspectives on acceptability may vary across different organizations. Next we'll move to another use case of face recognition for presence detection. 


## B.  Identify presence in office 

### PIC presence

In this case, my risks from wrongly defined person A as other person (FN), for example, are:
a. complain from employee
b. double handling presence data
==> In monetary terms, i will assume this as $10k per annum for manually correcting presence data.

As for the risks from wrongly defined other person as person A (FP), for example, are:
a. complain from employee
b. double handling presence data
==> In monetary terms, i will assume this as $10k per annum. This risks are the same with above 

Let's assume total benefit from annual cost saving of doing manual works and other tasks for around $ 50k. Using the same formula (expected risks) above we can estimate the expected benefit are: 

	EB 	= ((110+50)/200x $50k - (10/200 x $10k ) - (30/200 x $10k) - TC
		= $40k - $0.5k - $1.5k - TC
		= $ 38k - TC 
		~ 76% of total benefit retained
 
 The benefits are worthed and even using "not so accurate" method (accuracy only 80%), it will gave decent value for the organization because the risks are comparatively small and there are minimal follow up work occuring from the risks.
 
 
## Conclusion

Regardless that accuracy can change overtime due to many aspects, which I will cover in other article, prediction accuracy is just a measures. The interaction between business and organization should extend beyond mere measurement, as numerous factors come into play, each with its unique context. Different industries will inherently possess distinct perspectives, coupled with their own set of ethics, morale, costs and benefits. The illustration above underscores that accuracy is relative and context-dependent. The individuals best suited to comprehend this are those in internal organization who oversee and understand the entire industry process. They need to have a comprehensive understanding of the business goals, the accompanying risks, and the delicate trade-offs between privacy, fairness, accuracy, security, and ethics that businesses must navigate.

Considering all these risk considerations, it doesn't imply that we should completely avoid machine learning. On the contrary, management should actively embrace the opportunities it offers while ensuring that they effectively address the associated risks. Machine learning holds significant potential, but as this technology, along with other forms of AI, becomes integral to our economic and social structures, the risks it presents will intensify. For businesses, mitigating these risks may be just as crucial, if not more so, than managing the adoption of machine learning itself. Failure to establish proper practices to address these emerging risks may hinder companies from gaining traction in the business environment.







