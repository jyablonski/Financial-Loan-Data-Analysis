library(tidyverse)
library(tidymodels)
library(skimr)
library(extrafont)
library(scales)
library(vip)
library(ggrepel)
library(scales)
library(RColorBrewer)
library(themis)
library(corrplot)

dateVariable <- Sys.Date()
mdyDate <-  format(dateVariable, format = "%B %d, %Y")

# custom theme
theme_jacob <- function () { 
  theme_minimal(base_size=9, base_family="Gill Sans MT") %+replace% 
    theme(
      panel.grid.minor = element_blank(),
      plot.background = element_rect(fill = 'floralwhite', color = 'floralwhite')
    )
}

# rejected <- read_csv('rejected_2007_to_2018Q4.csv')
accepted <- read_csv('accepted_2007_to_2018Q4.csv')


accepted_main <- accepted %>%
  select(id, loan_amnt, grade, int_rate, loan_status, emp_length, term, home_ownership, purpose, addr_state,
         dti, fico_range_low, annual_inc) %>%
  filter(!id == 'NA') %>%
  separate(term, into = c('term1', 'term2'), sep = " ") %>%
  select(-term2) %>%
  mutate(term1 = as.numeric(term1)) %>%
  rename(term = term1)

accepted_sample <- accepted %>%
  sample_frac(0.00001)
# skim(accepted_main)

rm(accepted)

accepted_main %>%
  filter(!loan_amnt == 'NA') %>%
  ggplot(aes(loan_amnt)) +
  geom_histogram(fill = 'midnight blue', alpha = 0.8, color = 'black', bins = 40) +
  scale_y_continuous(labels = comma) +
  scale_x_continuous(labels = dollar_format()) +
  labs(x = 'Loan Amount',
       y = 'Count',
       title = 'Distribution of Loans - Average Loan is $15,047') +
  theme_jacob()


groups_avg <- accepted_main %>%
  group_by(grade) %>%
  summarise(avg_loan_amnt = mean(loan_amnt),
            avg_int_rate = mean(int_rate),
            avg_term = mean(term), 
            avg_fico = mean(fico_range_low))

groups_sum <- accepted_main %>%
  group_by(grade) %>%
  summarise(total_loan_amnt = sum(loan_amnt),
            total_loans = n()) %>%
  filter(!grade == 'NA')

# total loan amount by grades
groups_sum %>%
  ggplot(aes(grade, total_loan_amnt, fill = grade)) +
  geom_bar(stat = 'identity', show.legend = FALSE) +
  scale_y_continuous(labels = dollar_format()) +
  labs(x = 'Grade',
       y = 'Total Loan Amount',
       title = 'Total Loan Amounts in $') +
  theme_jacob()

# interest rate boxplot by grades plot.
accepted_main %>%
  filter(!grade == 'NA') %>%
  ggplot(aes(grade, int_rate, fill = grade)) +
  geom_boxplot(outlier.color = 'black') +
  scale_y_continuous(labels = percent_format(scale = 1), breaks = c(0, 5, 10, 15, 20, 25, 30)) +
  labs(x = 'Grade',
       y = 'Interest Rate',
       title = 'Interest Rates by Group') +
  theme_jacob()

states <- default_loans %>%
  group_by(addr_state, loan_status_main) %>%
  count() %>%
  arrange(desc(n)) %>%
  ungroup() %>%
  mutate(addr_state = fct_reorder(addr_state, n))

states %>%
  filter(!addr_state == 'IA') %>%
  ggplot(aes(n, addr_state, fill = loan_status_main)) +
  geom_col() +
  scale_x_continuous(labels = comma) +
  scale_fill_hue(direction = -1) +
  labs(x = 'Number of Loans',
       y = NULL, 
       title = 'What States are Receiving the Most Loans ?',
       fill = 'Loan Default') +
  theme_jacob() +
  theme(legend.position = 'top') +

# loan status breakdown plot.
accepted_main %>%
  filter(!grade == 'NA',
         !loan_status == 'Does not meet the credit policy. Status:Charged Off',
         !loan_status == 'Does not meet the credit policy. Status:Fully Paid',
         !loan_status == 'Default') %>%
  group_by(loan_status) %>%
  count() %>%
  ungroup() %>%
  mutate(pct_total = n / sum(n),
         pct_total = round(pct_total, 4)) %>%
  mutate(loan_status = fct_reorder(loan_status, n)) %>%
  ggplot(aes(n, loan_status, fill = loan_status, label = scales::percent(pct_total))) +
  geom_bar(stat = 'identity', show.legend = FALSE) +
  geom_text(position = position_dodge(width = 1),    # move to center of bars 
            hjust = -0.05,
            size = 3) +
  scale_x_continuous(labels = comma, breaks = c(0, 300000, 600000, 900000, 1200000), limits = c(0, 1200000)) +
  labs(x = 'Number of Loans',
       y = 'Loan Status', 
       title = 'Loan Status Breakdown (with % of Totals)') +
  theme_jacob()

accepted_main %>%
  group_by(emp_length) %>%
  count() %>%
  arrange(desc(n))

grade_employees <- accepted_main %>%
  group_by(emp_length, grade) %>%
  summarise(avg_loan_amnt = mean(loan_amnt),
            avg_int_rate = mean(int_rate))

# fico scores range from 600 - 850 only.
fico_bins <- accepted_main %>%
  mutate(bin = cut(fico_range_low, seq(min(fico_range_low), max(fico_range_low) + 4, 10), right = FALSE)) %>%
  group_by(bin) %>%
  summarise(avg_int_rate = mean(int_rate)) %>%
  ungroup() %>%
  filter(!bin == 'NA')

default_loans <- accepted_main %>%
  mutate(loan_status_main = case_when(loan_status == 'Charged Off' ~ 1,
                                      loan_status == 'Default' ~ 1,
                                      TRUE ~ 0),
         loan_status_main = as.factor(loan_status_main))
default_loan_stats <- default_loans %>%
  group_by(loan_status_main) %>%
  summarise(avg_int_rate = mean(int_rate),
            avg_loan_amnt = mean(loan_amnt),
            avg_term = mean(term))

bb <- default_loans %>%
  group_by(grade, loan_status_main) %>%
  count() %>%
  ungroup() %>%
  group_by(grade) %>%
  mutate(pct_total = n / sum(n),
         pct_total = round(pct_total, 4)) %>%
  filter(loan_status_main == 1)

chance_of_default <- bb %>%
  filter(loan_status_main == 1) %>%
  rename(chance_of_default = pct_total) %>%
  select(grade, chance_of_default)

default_data <- default_loans %>%
  group_by(grade, loan_status_main) %>%
  count() %>%
  ungroup() %>%
  group_by(grade) %>%
  mutate(pct_total = n / sum(n),
         pct_total = round(pct_total, 4)) %>%
  left_join(chance_of_default)

# number of defaults by grade plot.
default_data %>%
  ggplot(aes(grade, n, fill = as.factor(loan_status_main), label = scales::percent(pct_total))) +
  geom_bar(stat = 'identity', show.legend = FALSE) +
  scale_y_continuous(labels = comma) +
  geom_text(data = default_data %>% filter(loan_status_main == 1), vjust = -.6) +
  scale_fill_manual(values = c('chartreuse4', 'darkred')) +
  labs(x = 'Grade',
       y = 'Number of Loans',
       title = 'Number of Loans by Grade (with % Chance of Default)') +
  theme_jacob()

grade_employees <- accepted_main %>%
  group_by(emp_length, grade) %>%
  summarise(avg_loan_amnt = mean(loan_amnt),
            avg_int_rate = mean(int_rate),
            avg_term = mean(term))

# employment history plot.
grade_employees %>%
  ggplot(aes(avg_loan_amnt, avg_int_rate, color = grade)) +
  geom_point(size = 3) +
  facet_wrap(~emp_length) +
  labs(x = 'Average Loan Amount ($)',
       y = 'Interest Rate',
       title = 'Does Length of Employment History Impact Loan Amounts & Interest Rates ?') +
  theme_jacob() +
  guides(colour = guide_legend(nrow = 1)) +
  theme(legend.position = 'top')

purpose <- accepted_main %>%
  group_by(purpose) %>%
  summarise(count = n()) %>%
  mutate(purpose = fct_reorder(purpose, count))

purpose %>%
  filter(count >= 1500) %>%
  ggplot(aes(count, purpose, fill = purpose)) +
  geom_bar(stat = 'identity', show.legend = FALSE) +
  scale_x_continuous(labels = comma) +
  scale_fill_brewer(palette = 'Paired') +
  labs(x = 'Count of Loan Type',
       y = 'Loan Purpose', 
       title = 'What are People getting Loans for in this Dataset ?') +
  theme_jacob()

test <- log_dataset %>%
  mutate(high_int = case_when(int_rate >= 24 ~ '24% +',
                              int_rate >= 21 & int_rate < 24 ~ '21-24%',
                              int_rate >= 18 & int_rate < 21 ~ '18-21%',
                              int_rate >= 15 & int_rate < 18 ~ '15-18%',
                              int_rate >= 12 & int_rate < 15 ~ '12-15%',
                              int_rate >= 9 & int_rate < 12 ~ '9-12%',
                              int_rate >= 6 & int_rate < 9 ~ '6-9%',
                              TRUE ~ '6% or less'))

int_data <- test %>%
  group_by(high_int, loan_status_main) %>%
  count() %>%
  ungroup() %>%
  group_by(high_int) %>%
  mutate(pct_total = n / sum(n)) %>%
  filter(loan_status_main == 1) %>%
  ungroup() %>%
  mutate(high_int = fct_reorder(high_int, pct_total)) %>%
  arrange(desc(pct_total))

int_data %>%
  ggplot(aes(high_int, pct_total, label = scales::percent(pct_total))) +
  geom_bar(data = int_data %>% filter(loan_status_main == 1), stat = 'identity') +
  geom_text(data = int_data %>% filter(loan_status_main == 1), position = position_dodge(width = 1),    # move to center of bars 
            vjust = -0.4,
            size = 5) +
  scale_fill_hue(direction = -1) +
  scale_y_continuous(labels = percent_format()) +
  labs(x = 'Interest Rate', 
       y = '% Chance of Default',
       title = 'Effect of Interest Rates on Loan Defaults',
       fill = 'Loan Default') +
  theme_jacob()

term_data <- test %>%
  group_by(term, loan_status_main) %>%
  count() %>%
  ungroup() %>%
  group_by(term) %>%
  mutate(pct_total = n / sum(n)) 

term_data %>%
  ggplot(aes(term, n, fill = loan_status_main, label = scales::percent(pct_total))) +
  geom_bar(stat = 'identity') +
  geom_text(data = term_data %>% filter(loan_status_main == 1), position = position_dodge(width = 1),    # move to center of bars 
            vjust = -0.4,
            size = 5) +
  scale_x_continuous(breaks = c(36, 60)) +
  scale_fill_hue(direction = -1) +
  labs(x = 'Length of Term', 
       y = 'Number of Loans',
       title = 'Effect of Term Length on Loans (w/ % Chance of Default)',
       fill = 'Loan Default') +
  theme_jacob()

new_dataset <- default_loans %>%
  filter(loan_status == 'Current') %>%
  select(-id, -addr_state, -loan_status, -home_ownership, -emp_length, -grade) %>%
  mutate(term = as.numeric(term),
         purpose = as.factor(purpose)) %>%
  na.omit() %>%
  sample_n(size = 100000)

bb <- as.matrix(new_dataset)
str(bb)

test_dataset <- default_loans %>%
  filter(!loan_status == 'Current') %>%
  select(-id, -addr_state, -loan_status, -home_ownership, -emp_length) %>%
  mutate(term = as.numeric(term),
         purpose = as.factor(purpose),
         grade = as.factor(grade)) %>%
  na.omit() %>%
  group_by(loan_status) %>%
  count() %>%
  ungroup() %>%
  mutate(pct_total = n / sum(n))

# data modeling ----
set.seed(45)
log_dataset <- default_loans %>%
  filter(!loan_status == 'Current') %>%
  select(-id, -addr_state, -loan_status, -home_ownership, -emp_length) %>%
  mutate(term = as.numeric(term),
         purpose = as.factor(purpose),
         grade = as.factor(grade)) %>%
  na.omit() %>%
  sample_n(size = 100000)


loan_split <- log_dataset %>%
  initial_split(strata = loan_status_main)

loan_train <- training(loan_split)
loan_test <- testing(loan_split)


loan_rec <- recipe(loan_status_main ~ ., data = loan_train) %>%
  step_corr(all_numeric()) %>%
  step_dummy(all_nominal(), -all_outcomes()) %>%
  step_zv(all_numeric()) %>%
  step_normalize(all_numeric())

loan_prep <- loan_rec %>%
  prep()

loan_juiced <- juice(loan_prep)

log_spec <- logistic_reg() %>%
  set_engine(engine = 'glm') %>%
  set_mode('classification')

tune_spec <- rand_forest() %>%
  set_mode('classification') %>%
  set_engine('ranger', importance = 'permutation')

tree_spec <- decision_tree() %>%
  set_engine('rpart') %>%
  set_mode('classification')

knn_spec <- nearest_neighbor() %>%
  set_engine('kknn') %>%
  set_mode('classification')

svm_spec <- svm_poly() %>%
  set_engine('kernlab') %>%
  set_mode('classification')


tune_wf <- workflow() %>%
  add_recipe(loan_rec) %>%
  add_model(tune_spec)

tune_fit <- tune_spec %>%
  fit(loan_status_main ~ .,
      data = loan_juiced)

doParallel::registerDoParallel()

set.seed(234)
vfolds <- vfold_cv(loan_train, strata = loan_status_main)

glm_rs <- log_spec %>%
  fit_resamples(loan_rec,
                vfolds,
                metrics = metric_set(roc_auc, sens, spec, accuracy),
                control = control_resamples(save_pred = TRUE))

tune_rs <- tune_spec %>%
  fit_resamples(loan_rec,
                vfolds,
                metrics = metric_set(roc_auc, sens, spec, accuracy),
                control = control_resamples(save_pred = TRUE))

knn_rs <- knn_spec %>%
  fit_resamples(loan_rec,
                vfolds,
                metrics = metric_set(roc_auc, sens, spec, accuracy),
                control = control_resamples(save_pred = TRUE))

tree_rs <- tree_spec %>%
  fit_resamples(loan_rec,
                vfolds,
                metrics = metric_set(roc_auc, sens, spec, accuracy),
                control = control_resamples(save_pred = TRUE))

svm_rs <- svm_spec %>%
  fit_resamples(loan_rec,
                vfolds,
                metrics = metric_set(roc_auc, sens, spec, accuracy),
                control = control_resamples(save_pred = TRUE))




############

glm_rs %>%
  unnest(.predictions) %>%
  mutate(model = 'glm') %>%
  bind_rows(tree_rs %>%
              unnest(.predictions) %>%
              mutate(model = 'decision tree')) %>%
  bind_rows(knn_rs %>%
              unnest(.predictions) %>%
              mutate(model = 'knn')) %>%
  bind_rows(tune_rs %>%
              unnest(.predictions) %>%
              mutate(model = 'random forest')) %>%
  bind_rows(svm_rs %>%
              unnest(.predictions) %>%
              mutate(model = 'svm')) %>%
  bind_rows(final_res_xgb) %>%
  select(.pred_0, .pred_1, .pred_class, .row, loan_status_main, model) %>%
  group_by(model) %>%
  roc_curve(loan_status_main, .pred_0) %>% #  or .pred_0
  autoplot() +
  theme_light()

tune_fit %>%
  vip(geom = 'point', num_features = 25) +
  theme_jacob() +
  labs(title = 'Variable Importance')

glm_rs %>%
  collect_metrics()

glm_fit <- log_spec %>%
  fit(loan_status_main ~ ., data = loan_juiced)

glm_fit %>%
  tidy() %>%
  arrange(-estimate)

glm_fit %>%
  predict(
    new_data = bake(loan_prep, loan_test),
    type = "prob"
  ) %>%
  mutate(truth = loan_test$loan_status_main) %>%
  roc_auc(truth, .pred_0)

glm_fit %>%
  predict(
    new_data = bake(loan_prep, loan_test),
    type = "class"
  ) %>%
  mutate(truth = loan_test$loan_status_main) %>%
  spec(truth, .pred_class)

# new data
prediction_rs <- glm_fit %>%
  predict(
    new_data = bake(loan_prep, new_dataset),
    type = "prob"
  ) %>%
  mutate(prediction = case_when(.pred_1 > 0.5 ~ 1,
                                TRUE ~ 0)) 

prediction_rs %>%
  group_by(prediction) %>%
  count()

#### xg boost
xgb_spec <- boost_tree(
  trees = 1000, 
  tree_depth = tune(), min_n = tune(), 
  loss_reduction = tune(),                     
  sample_size = tune(), mtry = tune(),        
  learn_rate = tune(),                         
) %>% 
  set_engine("xgboost") %>% 
  set_mode("classification")

xgb_grid <- grid_latin_hypercube(
  tree_depth(),
  min_n(),
  loss_reduction(),
  sample_size = sample_prop(),
  finalize(mtry(), loan_train),
  learn_rate(),
  size = 30
)

xgb_wf <- workflow() %>%
  add_formula(loan_status_main ~ .) %>%
  add_model(xgb_spec)

vb_folds <- vfold_cv(loan_train, strata = loan_status_main)

doParallel::registerDoParallel()

set.seed(234)
xgb_res <- tune_grid(
  xgb_wf,
  resamples = vb_folds,
  grid = xgb_grid,
  control = control_grid(save_pred = TRUE)
)

xgb_res %>%
  collect_metrics() %>%
  filter(.metric == "roc_auc") %>%
  select(mean, mtry:sample_size) %>%
  pivot_longer(mtry:sample_size,
               values_to = "value",
               names_to = "parameter"
  ) %>%
  ggplot(aes(value, mean, color = parameter)) +
  geom_point(alpha = 0.8, show.legend = FALSE) +
  facet_wrap(~parameter, scales = "free_x") +
  labs(x = NULL, y = "AUC") +
  theme_light()

best_auc <- select_best(xgb_res, "roc_auc")
best_auc <- read_csv('best_auc.csv')
final_xgb <- finalize_workflow(
  xgb_wf,
  best_auc
)

final_xgb_fit <- final_xgb %>%
  fit(data = loan_train) %>%
  pull_workflow_fit()

final_xgb_fit %>%
  vip(geom = "point", num_features = 15) +
  theme_light()

final_res <- last_fit(final_xgb, loan_split)

final_res %>%
  collect_metrics()

final_res_xgb <- final_res %>%
  collect_predictions() %>%
  mutate(model = 'xgboost') %>%
  select(-id)

final_res %>%
  collect_predictions() %>%
  roc_curve(loan_status_main, .pred_0) %>%
  ggplot(aes(x = 1 - specificity, y = sensitivity)) +
  geom_line(size = 1.5, color = "midnightblue") +
  geom_abline(
    lty = 2, alpha = 0.5,
    color = "gray50",
    size = 1.2
  )
