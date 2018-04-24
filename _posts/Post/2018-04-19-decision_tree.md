---
layout: post
title:  "Decision Tree"
date:   2018-04-18 00:00:00
category: 인공지능
tags: decision-tree
---

classifier 1. decision tree

<!-- more -->

---

### class를 예측해보자.

다음과 같은 training set이 주어진다. age, income, studentm credit_rating이 주어질 때, buy를 하는지 안하는지를 나타낸 표이다.

 idx | age    | income| student| credit_rating| buys|
-----|--------|-------|--------|--------------|-----|
 0   | <=30   | high  | no     | fair         | no  |
 1   | <=30   | high  | no     | excellent    | no  |
 2   | 31...40| high  | no     | fair         | yes |
 3   | >40    | medium| no     | fair         | yes |
 4   | >40    | low   | yes    | fair         | yes |
 5   | >40    | low   | yes    | excellent    | no  |
 6   | 31...40| low   | yes    | excellent    | yes |
 7   | <=30   | medium| no     | fair         | no  |
 8   | <=30   | low   | yes    | fair         | yes |
 9   | >40    | medium| yes    | fair         | yes |
 10  | <=30   | medium| yes    | excellent    | yes |
 11  | 31...40| medium| no     | excellent    | yes |
 12  | 31...40| high  | yes    | fair         | yes |
 13  | >40    | medium| no     | excellent    | no  |

  
['<=30', 'low', 'yes', 'fair'] 경우에는 buy를 하게 될까?

---

### Heterogeneous → Homogeneous

decision tree의 기본 아이디어는 Heterogeneous(=뒤죽박죽)한 상태를 Homogeneous(=pure)한 상태로 분류해 나가는 것이다. 데이터를 어떤 attribute로 나누어야 더욱 pure한 상태가 될 지를 계산해서 최고를 선택한다. Greedy한 방법이다. 일반적으로 Top-down방식으로 트리를 구성해 나간다.


---

### Attribute 선택

attribute 선택을 위해서 데이터가 얼마나 pure한지를 계산하는 방법을 정의해 주어야 한다. Decision Tree를 구성하는 방법에 따라 다양한 방법들이 있다.

**1. Information Gain**

ID3 - C4.5 - C5.0 계열의 decision tree에서 사용된다. 트리의 각각 노드가 얼마나 pure한지를 Entropy라는 개념을 통해 계산한다. Entropy는 물리에서 나오는 불확실성이란 개념을 차용한(?)것이다. Entropy는 다음과 같이 계산한다.

$$Entropy = Info(D) = -\sum_{n=1}^{m}p_i log_2(p_i)$$  

$$m$$은 class의 개수, $$i \in \{1,2,...,m\}$$, $$f_i$$는 $$i$$로 표시된 집합 안의 항목이 나타날 확률이다. 참고로, Entropy는 최대 1(가장 불순한 상태)부터 0(가장 순수한 상태)까지의 값을 가진다.

트리를 구성함는 과정을 state에서 다음 state로 나아가는 과정이라고 생각하자. 각 state에서의 Entropy를 Information이라고 하고, Information의 차이가 크게 나도록 Attribute를 선택하게 된다.

$$Info_A(D) = -\sum_{j=1}^{v} \left| \frac{D_j}{D} \right| \times Info(D_j)$$  

Attribute 'A'로 나뉘게 된 state의 Information은 다음과 같이 구한다. attribute로 나뉘게 된 data의 사이즈만큼 weight를 줘서 각각 노드의 Entropy를 평균을 낸다고 생각하면 된다.

$$Gain(A) = Info(D) - Info_A(D)$$

Attribute 'A'로 나눴을 때 Gain은 다음과 같고, 트리는 Gain을 가장 크게 하는 attribute를 선택하여 가지를 뻗는다.


**2. Gain Ratio**

Information Gain은 약점이 있다. Attribute가 가진 value가 많을 수록 더 높은 Gain을 얻어낼 경향이 크다는 것이다. 더 많이 나눴으니 pure할 확률이 높아지는 것은 어찌보면 당연하다. 따라서 얻어낸 Gain을 SplitInfo로 나눠준 값(Gain Ratio)를 사용하는 방법을 사용하는 것을 고려할 수 있다.

$$SplitInfo_A(D) =  -\sum_{j=1}^{v} \left| \frac{D_j}{D} \right| \times log_2(\left| \frac{D_j}{D} \right|)$$  

SplitInfo는 Attribute가 가진 value가 많을 수록 높은 값을 가지게 된다. 따라서 많은 value를 가진 Attribute에 페널티를 주는 방식이라고 생각하면 된다.

하지만 Gain Ratio는 partition의 개수가 적다는 이유만으로 Attibute를 선택하게 되는 우를 범할 가능성도 있다.


**3. Gini Index**

gini는 얼마나 purity한가를 나타낸다. Information Gain에서 Entropy대신에 gini라는 측정방식을 사용하는 데, 다음과 같이 구한다.

$$gini(D) = 1 - \sum_{j=1}^{n}p_j^2$$

**4. 트리 예시**

맨 처음의 training 데이터를 Information Gain을 이용하여 decision tree를 구성하면 다음과 같이 모델이 만들어진다.
![dtree0]({{site.url}}/asset/artificial/dtree0.png){:height="200px" .center-image}  

`이진트리`로 만들 수도 있다. `attribute = value ? True : False` 와 같은 방법으로 나누어서 트리를 구성해나간다.
![dtree1]({{site.url}}/asset/artificial/dtree1.png){:height="350px" .center-image}  

---

### Overfitting

훈련 데이터 세트의 모든 샘플에 완벽하게 맞도록 트리가 설계 될 때과 fit하다고 표현한다. 위에서 이진트리를 보면 쉽게 이해가 갈 것이다. 하나의 데이터를 설명하고자하는 룰이 너무 디테일 하다는 의미이다. 이렇게 트리를 구성하다보면, 정확도에도 문제가 생길 뿐더러 어떤 데이터는 class가 구분되지 않는 경우도 생긴다.

이를 위한 해결방안으로 Pruning을 사용한다. 트리의 완성을 기준으로 언제 pruning을 진행하냐에 따라 크게 Prepruning과 Postpruning으로 구분이 된다.

1. Prepruning의 방법으로는 일정 depth까지만 트리를 구성하는 방법 등이 있다.
2. Postpruning의 방법으로는 subtree-replacement, rule-post pruning등이 있다.

---

### scikit-learn을 통한 decision tree의 사용

다음과 같이 사용한다. 입력 데이터에 대한 간단한 가공이 필요하다. makeData는 임의로 만든 함수이고 코드에서는 생략했다. attribute의 value들이 categorical 하다면 numeric하게 바꿔줘야한다. scikit-learn자체에서 이를 위한 API도 제공하는 듯 하다. graphviz는 만든 트리를 이미지로 뽑아주는 신기한 툴이다. 

{% highlight python linenos %}
from sklearn import tree, preprocessing
from sklearn.metrics import accuracy_score
import graphviz
import sys

train = open(sys.argv[1],"r")

label = train.readline()[:-1].split('\t')
datum = [line.split('\t') for line in train.read().splitlines()]

# Processing of data
# format : [ [1,2, ... ,1], [1,0, ... ,4], ... ,[3,2, ... ,2] ]
data, target = makeData(datum)

clf = tree.DecisionTreeClassifier(criterion='entropy')
clf = clf.fit(data, target)

dot_data = tree.export_graphviz(clf, feature_names=label[:-1], class_names= ['no','yes'] ,out_file=None)
graph = graphviz.Source(dot_data)
graph.render("buy")

# 다음과 같이 데이터가 어떤 클래스에 속할지 predict할 수 있다.
# classified_data = clf.predict([0,3,2,0])
{% endhighlight %}

다음과 같은 결과를 출력한다. 
![dtree_sci]({{site.url}}/asset/artificial/dtree_sci.png){:height="350px" .center-image}  

---

### Decision Tree의 구현

이진 트리로 구현했으며, 예제로 쓴 training data가 categorical해서 이를 numerDict과 encode를 통해 numeric하게 바꿔주었다.

training과 test data는 각각 다음과 같은 format이다. 첫줄에는 attribute의 name이 있고, 둘째줄부터 data의 value들이 들어온다. 각각의 구분은 tab으로 되어있다.

[dataset]({{site.url}}/asset/artificial/dt_train1.txt)의 링크이다.

{% highlight python linenos %}
import sys
import math
from random import random
from collections import defaultdict

# common value
attr = defaultdict(list)
res = []

# Categorial Value to Numeral Value
numerDict = {
	"<=30" : 0, "no" : 0, "fair" : 0, 'low': 0, 'small' : 0, "0" : 0, 
	"31...40" : 1, "yes" : 1, "excellent" : 1, 'med': 1, 'medium' : 1, "1" : 1,
	">40" : 2, 'high' : 2, 'big' : 2, "2" : 2,
	'vhigh' : 3, '3' : 3,
	'4' : 4,
	'5more' : 5, 'more' : 5
}

# node of Decision Tree
class node :
	def __init__(self, dataSet, ruleList):
		self.data = dataSet

		# Number of each Class in node
		self.ansCnt = defaultdict(int)

		# Binary Tree (true Node & false Node)
		self.tNode = None
		self.fNode = None
		
		# divide dataSet using this attribute
		self.attrKey, self.splitPoint = '' , 0

		# possible attribute to use
		self.ruleList = ruleList

		# Properties
		self.info = 0
		self.gini = 1
		self.pure = False
		self.leaf = True
		self.dataSize = len(self.data)

		self.calculateNode()

	# Draw properties
	def calculateNode(self):

		# find index of result column
		idx = len(list(attr.keys()))
		total = self.dataSize

		# Make ansCnt
		for data in self.data:
			self.ansCnt[data[idx]] = self.ansCnt[data[idx]] + 1

		# calculate info of class
		for key in self.ansCnt:
			frac = float(self.ansCnt[key])/total
			self.info = self.info - (frac * math.log2(frac))
			self.gini = self.gini - (frac * frac)

		# All samples belong to same class
		if len(list(self.ansCnt)) == 1:
			self.pure = True
	
	# chk for remaining attribute
	def updateNode(self, attrKey, splitPoint):
		self.attrKey, self.splitPoint = attrKey, splitPoint

		test = []
		for _key in attr:
			test.append(self.ruleList[_key])

		if test == [] or attrKey == '':
			return False
		else:
			return True

	# No insertion --> leaf Node
	def insertNode(self, tNode, fNode):
		self.leaf = False
		self.tNode = tNode
		self.fNode = fNode

	# Get Result
	def classifiedResult(self):
		idx = len(list(attr.keys()))
	
		if self.dataSize == 0:
			#print("Not enough data for this case!")			
			#return self.parent.getMajority()
			return 'None'

		elif self.pure:
			return self.data[0][idx]
	
		else:
			return self.getMajority()

	# get the majority among data set
	def getMajority(self):
		ret = sorted(self.ansCnt, key = self.ansCnt.get, reverse = True)
		return ret[0]

	def getMajorityNum(self):
		if self.dataSize == 0:
			return 0
		else:
			return self.ansCnt[self.getMajority()]


# Decision Tree Main
class dtree:
	def __init__(self, dataSet):
		# seperate dataSet (attribute <-> data)
		self.data = dataSet
		self.ruleList = attr

		# root node
		self.node = node(self.data, attr)


	# node (attrKey <= splitPoint) : trueNode & falseNode
	def makeNode(self, curNode, attrKey, splitPoint):

		trueDataSet = []
		falseDataSet = []

		labels = list(attr.keys())
		
		for trans in curNode.data:
			if trans[labels.index(attrKey)] <= splitPoint:
				trueDataSet.append(trans)
			else :
				falseDataSet.append(trans)

		tList, fList = [], []
		for i in curNode.ruleList[attrKey]:
			if i <= splitPoint:
				tList.append(i)
			else:
				fList.append(i)

		# Re-calculate ruleList (Can't use rule which has already been used)
		tmp = curNode.ruleList.copy()
		tmp.pop(attrKey)

		trList = tmp.copy()
		trList[attrKey] = tList

		frList = tmp.copy()
		frList[attrKey] = fList


		return node(trueDataSet, trList), node(falseDataSet, frList)


	# cacaulte information gain with other node &
	# decide which attribute to use
	def selectAttr(self, curNode):

		info = curNode.info
		total = len(curNode.data)

		'''
		# information gain
		max = 0
		retAttr, splitPoint = '', 0
		for attrKey in list(self.ruleList.keys()):
			for split in chooseList(attrKey, self.ruleList):
				tNode, fNode = self.makeNode(curNode, attrKey, split)
				n1 = float(len(tNode.data))
				n2 = float(len(fNode.data))
				gain = info - ((n1/total)*tNode.info + (n2/total)*fNode.info)

				if max < gain:
					max = gain
					retAttr, splitPoint = attrKey, split
		'''
		# Gini
		max = 0
		retAttr, splitPoint = '', 0
		for attrKey in list(self.ruleList.keys()):
			for split in chooseList(attrKey, self.ruleList):

				tNode, fNode = self.makeNode(curNode, attrKey, split)
				n1 = float(len(tNode.data))
				n2 = float(len(fNode.data))
				gini = curNode.gini - ((n1/total)*tNode.gini + (n2/total)*fNode.gini)
		
				if max < gini:
					max = gini
					retAttr, splitPoint = attrKey, split
		
		return retAttr, splitPoint


	# cacaulte information gain with other node
	def makeTree(self, node):
		if len(node.data) == 0:
			return
	
		attrKey, splitPoint = self.selectAttr(node)

		if node.updateNode(attrKey, splitPoint):

			tNode, fNode = self.makeNode(node, attrKey, splitPoint)
			node.insertNode(tNode, fNode)

			if node.pure == False:
				self.makeTree(tNode)
				self.makeTree(fNode)


# Predict Class when testData(type : list) is given
# [0, 3, 2, 1, 3] => 'ucc'
def predict(model, testData):

	labels = list(attr.keys())
	node = model.node
	while(not node.leaf):
		attrKey = node.attrKey
		splitPoint = node.splitPoint

		if testData[labels.index(attrKey)] <= splitPoint:
			node = node.tNode
		else:
			node = node.fNode

	return node.classifiedResult()
	

# Read Test File and make output file which have classified data
# [[1,2,3], [2,3,3]] => outputFile
def predictDataSet(model, test, output):

	label = test.readline()
	output.write(label)

	for line in test.read().splitlines():
		output.write(line)
		output.write('\t')

		e = encode(line.split('\t'))

		output.write(predict(model, e))	
		output.write('\n')


# Get list of possible split point
# [2, 4, 5] ==> [3.0, 4.5]
def chooseList(attrKey, ruleList):
	l = ruleList[attrKey]
	return [(l[i] + l[i+1]) / 2 for i in range(len(l) - 1)]


# Read Input file and process data
# datum : list of each transaction
# attr : data type for managing Label(Category)
#	=> {'buying':['low', 'med', 'high'], 'maint':['low', ...], ...}
# res : Label
def makeDataSet(dataSet):
		
	datum = []

	for line in dataSet:
		data = line[:-1].split('\t')
		a = encode(data[:-1])
		a.append(data[-1])
		datum.append(a)

	labels = datum.pop(0)
	
	for data in datum:
		for label in labels:
			idx = labels.index(label)
			tmp = attr[label]
			if data[idx] not in tmp:
				attr[label] = sorted(tmp + [data[idx]])

	res = attr.pop(labels[-1])

	return datum, attr, res


# ['<=30', 'high', 'no', 'fair'] => [0,0,0,0]
def encode(dataSet):

	data = []
	for item in dataSet:
		if item in numerDict:
			data.append(numerDict[item])
		else:
			data.append(item)

	return data


# main function
if __name__ == "__main__":

	training = open(sys.argv[1],"r")
	test = open(sys.argv[2], "r")
	output = open(sys.argv[3], "w")

	train, attr, res = makeDataSet(training)

	model = dtree(train)
	model.makeTree(model.node)

	predictDataSet(model, test, output)


{% endhighlight %}
