# The application logic

The product will have to manage different **models** which may potentially be applied in different **contexts**. What comes to mind when thinking of a 'data-driven product' is usually a machine learning model in production. But it's more subtle than that. For example the product may not use machine learning at all (actually advisable in the beginning, in many cases). Some domain-scpecific constraints may need to be enforced on top of any production model. I would break down the types of application logic in the following way:
1. **model logic**: aimed at improving our target KPI. This is typically a machine learning model trained on the set of features that we feed it, but can include heuristics on top as long as their goal is to 
2. **domain rules**: these are applied on top of any model 
3. **business rules**: these are applicable on top of models, but on a configuration level


....
