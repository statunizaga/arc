# arc
Association Rule Classification

This package for R implements the Classification based on Associations algorithm (CBA):

 ```Liu, B. Hsu, W. and Ma, Y (1998). Integrating Classification and Association Rule Mining. Proceedings KDD-98, New York, 27-31 August. AAAI. pp 80-86.```


## Features 
- Pure R implementation* 
- Supports numerical attributes
- No meta parameters with automatic tuning of support and confidence thresholds (optional)**

NOTES: * Requires the arules package for the rule generation step ** There are some metaparameters for automatic tuning, but in most cases the default values are OK.

## Other use cases
- `prune` function can be used to reduce the size of a rule set learnt by the apriori function from the  arules package
- `topRules` function can be used as a wrapper for apriori allowing to mine for a user specified number of rules.

## Examples

### Complete classification workflow
```R
# note that the iris dataset contains numerical features
data(iris)
train <- iris[1:100,]
test <- iris[101:length(iris),]
classatt <- "Species"
# learn the classifier
rm <- cba(train, classatt)
prediction <- predict(rm, test)
acc <- rulemodelAccuracy(prediction, test[[classatt]])
print(acc)
```

### Mine Predefined Number of Rules with apriori
The arules documentation gives the following example:
```R
data("Adult")
## Mine association rules.
rules <- apriori(Adult,
parameter = list(supp = 0.5, conf = 0.9, target = "rules"))
summary(rules)
```
This returns 52 rules. The default value for the minlen and maxlen parameters unspecified by the user was 1 and 10. 
Assuming that the user wishes to obtain 100 rules, this can be achieved with the arc package as follows:

```R
data("Adult")
rules <- topRules(Adult, target_rule_count=100, init_support=0.5,init_conf=0.9, minlen=1, init_maxlen = 10)
summary(rules)
```
This will return 100 rules. The mechanics behind are  iterative step-wise changes to the initial values of the provided thresholds. In this case, there will be nine iterations, the minimum confidence threshold will be lowered to 0.65 and the final rule set will be trimmed.

In order to keep the number of iterations and thus run time low, it might be a good idea to set the `init_maxlen` parameter to a low value:
```R
data("Adult")
rules <- topRules(Adult, target_rule_count = 100, init_support = 0.5,init_conf = 0.9, minlen = 1, init_maxlen = 2)
summary(rules)
```
In this case, the algorithm finished after 11 iterations. However, in my experience, lower initial value is a safer bet.


