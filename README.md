LoanChecker

A loan model with a fairness audit built around it. On 1970s German loan data, it measures how often reliable under-25s are approved compared with reliable older applicants, traces why, and tests what closing the gap costs.

This project is a prototype and is not financial advice.

Built for the Veritas AI International High School Hackathon 2026, Finance track, starting from suggested project 4.2.3 (Fairness audit of a loan model). Team: Prajjayan Thoppey, Aariv Johar.

Links
What	Where
Final notebook (Google Colab, view only, outputs saved)	https://colab.research.google.com/drive/1H7KfMA1h194yUPkqJ_8DmW6d4ofxwMoz
Final notebook in this repository (same notebook, outputs saved)	LOANCHECKER.ipynb
Recorded output of a full run	LoanCheck_all_results.txt
Project website	https://programrizz.github.io/LoanChecker_v2.0/
Write-up	https://docs.google.com/document/d/1lb8RGbIMvVaPseB_OrZD647O1XeeXrJ-JQaBSNquw-c
Which notebook is final
LOANCHECKER.ipynb is the final, verified analysis. It is the same notebook as the Colab link above. It has 110 cells: 58 text cells and 52 code cells. The code cells were run once, top to bottom, in order (execution counts 1 to 52, no errors), and every output is saved. LoanCheck_all_results.txt is its printed summary.
loan_fairness_audit_FINAL_v3.ipynb is an earlier draft and is out of date. It was saved with no outputs, has 88 cells against the final's 110, and is missing later sections such as the UK lending comparison. Don't use it to check any number in this README or on the website.
The question

A loan model can save a bank money and still turn away reliable people because of who they are. Young adults are most exposed: short job histories, often living with family and little time to build a record. We asked whether reliable under-25s are refused more often than reliable older applicants, why, and whether a bank could close that gap without losing money. Our user is a UK lender's risk or compliance team checking a model before it goes live.

Data

Statlog German Credit (UCI, dataset 144): 1,000 granted loans from one southern German bank, 1973 to 1975, with 20 details each. 700 were repaid and 300 were not. 149 applicants were under 25, of whom 88 repaid. Refused applicants aren't in the data.

The standard codebook mislabels several categories, so we matched the records against the corrected South German Credit data (Grömping 2019). We matched records on loan length, amount, age and outcome: 990 of the 1,000 matched, and in those 990 each code lined up with one corrected category (apart from 1 housing record and 10 foreign worker records, where the two versions disagree). The other 10 records differ on at least one of those four fields, so they weren't checked one by one. We didn't change any data values. We only renamed each code, using the corrected meaning, for all 1,000 records. The numbers don't change; the explanations do (for example, the code usually called "no checking account" really means "200 DM or more, or a salary account").

Method
Models: logistic regression, a random forest and gradient boosting. Each was trained on 800 applicants and scored on the 200 it hadn't seen, five times over (5-fold cross validation, with settings tuned inside the training folds). Every result below comes from those unseen predictions.
Lending line: the dataset's documentation says a bad loan costs 5 times as much as turning away a good customer. Lending is cheaper only when the chance of repaying is above 5 ÷ 6 = 83.3%. We fixed this line before seeing any results. We also report the usual 50% line, which gives higher accuracy but a much higher cost.
Model choice: the three costs (547, 553 and 552) were within chance of each other, so by a rule written before any fairness results we picked the one whose reasoning can be read: logistic regression. It also had the smallest age gap among reliable borrowers (19.5 points, against 31.8 for the random forest and 26.9 for gradient boosting).
Uncertainty: brackets are 95% ranges from re-drawing the applicants 2,000 times.
Results
Policy	Accuracy	Approved, under 25	Approved, 25+	Overall gap (points)	Cost
Baseline: approve everyone	70.0%	100%	100%	0	1,500
Baseline: refuse everyone	30.0%	0%	0%	0	700
Our model, lending above 50%	75.0%	67.1%	82.0%	14.9 (7.2 to 23.0)	946
Our model, lending above 83.3%	58.5%	18.8%	38.0%	19.2 (12.0 to 26.1)	547
With our fix	59.7%	30.9%	38.0%	7.1 (−1 to 16)	547

Cost is 5 points per bad loan made and 1 point per good customer turned away, over 1,000 applicants; lower is better. In this sample refusing everyone is cheaper than approving everyone, but only because 30% of loans went bad. At the real rate of about 5% it would flip.

Three gaps, named:

Overall gap: 19.2 points (18.8% against 38.0% of all applicants approved).
Gap among reliable borrowers (main measure): of the people who really repaid, 28.4% of under-25s and 47.9% of older applicants were approved, a gap of 19.5 points (8.8 to 29.7). For age lines from 22 to 35 it stays between 14.2 and 26.8, and the overall gap between 15.6 and 19.5.
Gap between similar finances (same account status and savings, loan size and length on the same side of the median): 11.3 of the 19.2 overall points remain, and 13.6 of the 19.5.

Causes. Current account (0.22 on the model's score), age itself (0.20), family status (0.12) and housing (0.11) push young applicants down most; a few details push the other way, for a net 0.61. Deleting age only moved the overall gap from 19.2 to 17.9 (19.5 to 17.8 among reliable borrowers), because family status, housing and job length give age away: from them alone a second model picked the younger of two applicants 80% of the time.

The fix. For each fifth of the data, we used the other four fifths to find the highest under-25 line that gets young applicants approved at least 80% as often as older ones (lines of 74% to 79%, about 76%). The 80% target comes from a US rule of thumb, not UK law. The gap among reliable borrowers falls from 19.5 to 2.4 points (−8.9 to 14.0). Of 18 extra young approvals, 15 repaid, exactly the 5 in 6 break-even rate, so cost stays at 547 (change −17 to +21). If the model's chances are honest the fix should cost about 4.5 points on average, too small to measure here. A lower line for everyone would cost 521 but leave the reliable-borrower gap at 19.7. The fixes that don't use age when deciding narrow it less: deleting age and its three best stand-ins (10.2), and deleting age while reweighting training so age and repaying are unrelated (12.8, which still needs age to set the weights).

Where it fails, and why
An example it gets wrong: applicant #144 is 23, has the best current account category (200 DM or more, or a salary account) and repaid a 2,288 DM loan over 21 months. The model gives them 82.8%, just under the line, so it refuses them (it also refused them while they were hidden in testing). The main reasons against were their instalment rate band (4 of 4), the family status code shared by single men and non-single women, and no savings on record. At 26, with nothing else changed, they'd be approved. Our fix approves them.
A modest model: it ranks a repayer above a non-repayer 79% of the time, and turns away 382 of the 700 people who would have repaid. There's no income or debt data.
Wrong bad-loan rate: at the reported real rate of 5%, the overall gap shrinks from 19.2 to 7.9 points (2.1 to 14.3) and the gap among reliable borrowers from 19.5 to 4.5 (−0.9 to 11.1), which we can't tell apart from zero. Young applicants are still approved less, but by how much depends on this rate.
Small groups: only 88 young applicants repaid, so the ranges are wide. Across all under-25s the model predicted 61.3% would repay and 59.1% did, within chance, but a mismatch of up to 10 points could hide in a sample this size.
The fix moves the unfairness: 122 applicants aged 25 or older stay refused with scores that now get a younger person approved, and 106 of them repaid.
Recession risk: across 20,000 simulated futures, the loss exceeded only 1 time in 100 rises from 36.5% to 37.2% of money lent under the fix. Both are too high (30% bad-loan rate, whole loan lost), the recession strength is our assumption, and this run used one under-25 line (0.76) chosen on all the data.
UK law (our reading as students, not legal advice): the Equality Act lets financial services treat people differently by age if a risk assessment that uses age rests on relevant, reliable information (Schedule 3, paragraph 20A); whether our lower line fits is a question for a lawyer. Our model also uses a detail that reveals sex for 690 of 1,000 applicants. Sex is protected in UK lending with no such exception, so a UK version would drop it (marital status alone isn't protected for services). We haven't measured what that changes.
Testing limits: no second untouched test set, and the fix's line was picked using other folds' predictions from models that had seen the test fold, so a little optimism could remain.
What we'd do next
Repeat the audit on a large, recent dataset with refused applicants, income, outgoings and the real default rate.
Test gentler fixes: phasing the lower line out between 25 and 30, and a version without the detail that reveals sex.
Reproducing the analysis

Open the Colab link above, choose File, Save a copy in Drive, then Runtime, Run all. The notebook installs ucimlrepo and downloads both datasets itself. It was run in Google Colab's standard Python 3 runtime in November 2026; the notebook doesn't record library versions.

Sources

Data

Statlog (German Credit Data), UCI Machine Learning Repository, loaded with fetch_ucirepo(id=144): https://archive.ics.uci.edu/dataset/144/statlog+german+credit+data
South German Credit (UPDATE), UCI Machine Learning Repository, for the corrected labels: https://archive.ics.uci.edu/dataset/573/south+german+credit
Grömping, U. (2019). South German Credit Data: Correcting a Widely Used Data Set. Report 4/2019, Beuth University of Applied Sciences Berlin. Also the source of the 5% real bad-loan rate: http://www1.beuth-hochschule.de/FB_II/reports/Report-2019-004.pdf

Tools. No pretrained models; all three models were trained from scratch. Google Colab (https://colab.research.google.com), pandas (https://pandas.pydata.org), NumPy (https://numpy.org), SciPy (https://scipy.org), scikit-learn (https://scikit-learn.org), Matplotlib (https://matplotlib.org), ipywidgets (https://ipywidgets.readthedocs.io), ucimlrepo (https://pypi.org/project/ucimlrepo).

Methods and references

Kamiran, F. and Calders, T. (2009). Classifying without discriminating. 2nd International Conference on Computer, Control and Communication. They split this dataset at 25 or younger; we used under 25 (at their line our gaps are 19.3 overall and 20.5 among reliable borrowers).
Kamiran, F. and Calders, T. (2012). Data preprocessing techniques for classification without discrimination. Knowledge and Information Systems 33, 1 to 33, for reweighing: https://doi.org/10.1007/s10115-011-0463-8
Hardt, M., Price, E. and Srebro, N. (2016). Equality of Opportunity in Supervised Learning. NeurIPS. Our "approval among people who repaid" measure is their equal opportunity idea.
Equality Act 2010, section 28 (https://www.legislation.gov.uk/ukpga/2010/15/section/28) and Schedule 3, paragraph 20A (https://www.legislation.gov.uk/ukpga/2010/15/schedule/3/paragraph/20A); FCA Consumer Duty (https://www.fca.org.uk/firms/consumer-duty/about) and CONC 5.2A (https://www.handbook.fca.org.uk/handbook/CONC/5/2A.html). Our reading as students, not legal advice.

AI tools

Claude (Anthropic), https://claude.ai: wrote most of the notebook code to our specification, built the project website, and helped draft and check the write-up. We chose the questions, checked the results and can explain every step.
ChatGPT (OpenAI), https://chatgpt.com: helped us review our methodology, interpret results, check the write-up and prepare the project.
Disclaimer

This project is a prototype and is not financial advice. LoanChecker is an experimental research prototype built on 1970s public data. It is not a credit scoring system and must not be used to make real lending decisions.
