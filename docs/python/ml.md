# machine learning

## linear regression

```python
import statsmodels.api as sm

# y = ax + c
X = df['test_segments'].astype(float)
X = sm.add_constant(X)
Y = df['mde']
model = sm.OLS(Y, X).fit()
print(model.summary())


# y = ax
X = df['test_segments'].astype(float)
Y = df['mde']
model = sm.OLS(Y, X).fit()
print(model.summary())
```

## exponential fitting

```python
def fit_exp(df, p0=(1, 0.01)):


   # y = a * e^(bx)
   def exponential_model(x, a, b):
       return a * np.exp(b * x)
  
   popt, pcov = curve_fit(exponential_model, df['segment_size'].astype(float), df['mde'], p0)
   yhat = exponential_model(df['segment_size'].astype(float), *popt)
   # x_ext = pd.Series(np.arange(100, 1700, 100).astype(float).tolist())
   # yhat_ext = exponential_model(x_ext, *popt)

   res = pd.DataFrame(zip(df['segment_size'], df['mde'], yhat), columns=['size', 'mde', 'mde_pred'])
   rmse = np.sqrt(np.mean((res['mde'] - res['mde_pred']) ** 2))

   # print(f"""
   #     \033[1m "exponential model" \033[0m
   #     y = a * e^(bx)
   #     RMSE: {rmse:.6f}
   #     MDE = {popt[0]:.6f} * e ^ ({popt[1]:.6f}) * SIZE
      
   #     \033[1m "segment size calculator" \033[0m
   #     SIZE = ln[ MDE / ({popt[0]:.6f}) ] / ({popt[1]:.6f})""")

   return popt, yhat, rmse


def fit_exp_size(mde, params):
   size = int(np.log( mde / params[0] ) / params[1])
   # print(f"""
   #     segment size for {mde*100:.2f}%:
   #     \033[1m {size} \033[0m """)
   return size

```