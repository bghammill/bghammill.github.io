The `babies` data set contains observations about the birthweight and other characteristics of children born in the San Francisco Bay area from 1960–1967.

We would like to build a model for birthweight as a function of the mother’s age and whether this child was her first (`parity == 0`). Use the mathematical specification below to code the model in R.



birthweight=β0+β1⋅age+β2⋅parity+ϵbirthweight=β0+β1⋅age+β2⋅parity+ϵ



The birthweight variable is recorded in the column `bwt`.

- Use `lm()` to build the parallel slopes model specified above. It’s not necessary to use `factor()` in this case as the variable `parity` is coded using binary numeric values.
- The formula syntax is based on the mathematical expression of the model. For example, given the model y=β0+β1⋅x+β2⋅zy=β0+β1⋅x+β2⋅z, the corresponding formula is `y ~ x + z`.

R Code

### Syntax from plot

This time, we’d like to build a model for birthweight as a function of the length of gestation and the mother’s smoking status. Use the plot to inform your model specification.



