---
title: A Dynamic Task Prioritization Formula
categories : [Algorithms]
tags :  [productivity,javascript,maths,notion]
description: A flexible, formula-based approach to task prioritization that adapts with time.
comments: true
math: true
media_subpath: /assets/task-priority/
---

For the past three years, I have been using a formula for calculating the priority of tasks on my to-do list. To maximize my productivity, I do not like wasting time thinking about the next task to tackle each time I open my to-do list. Therefore, I came up with a formula to calculate the priority of my tasks and then sorted my to-do list in descending order of priority. Unlike other methods of task prioritization, my formula takes time into consideration and dynamically updates the priority.

## Past attempts at task prioritization

### Static priority

When I first started incorporating task prioritization in my to-do list, I stored a priority variable in each task. I would manually enter a priority (1-10) for each task based on the current priority of other ongoing/upcoming tasks. However, this method required me to manually update the priorities of tasks with time. For example, as a task approached its deadline, I had to manually increase the priority otherwise I might miss it.

### Eisenhower matrix

<img src="eisenhower.png" height ="446" width="702" alt = "Eisenhower matrix">
_Eisenhower matrix (Nutt, no date)_

This method is quite popular for determining task prioritization. However, I had some issues with it:

1. Repeatedly applying this framework over time for a large number of tasks can be mentally exhausting.
2. It is too binary. It does not account for varying degrees of importance and urgency. 
3. Importance can also vary with time and the onus is on you to constantly reevaluate the tasks.
4. When multiple tasks fall into the same quadrant (like important & urgent), you are forced to choose between them which can lead to choice paralysis.

## Requirements of new formula

I wanted my task priority formula to cater for the following criteria:

1. The closer a task is to completion, the higher priority it should have. The goal is to prevent me from starting multiple tasks at once and then not completing them due to other incoming high priority tasks.
2. Tasks with the highest priority, irrespective of time left, should have higher priority.
3. Harder tasks (tasks with a high `xHours`) should have higher priority. If two tasks are tied, I prefer working on hard tasks first. 

## Parameters

The priority of a task should be a function of the following variables:

| Variable     | Description                                                    | Data Type | Constraints                 |
| ------------ | -------------------------------------------------------------- | --------- | --------------------------- |
| `Due Date`   | Deadline of task                                               | Date      |                             |
| `Progress`   | Percentage of task completed                                   | Integer   | Between 0 and 100 inclusive |
| `Importance` | Importance of task                                             | Integer   | Between 0 and 10 inclusive  |
| `xHours`     | Expected number of hours of work required to complete the task | Float     | Non-zero value              |

## Formula

The priority of an incomplete task can be calculated as follows:

$$
\text{Priority} = \frac{
\left( \frac{1 + \text{Progress (%)} }{100} \right)^{1.6}
\cdot
\left( \frac{1 + \text{Importance} }{10} \right)^3
\cdot
\left( \text{xHours} \right)^{1.3}
}{
\begin{cases}
\text{Days left}, & \text{if } \text{Days left} > 0 \\
\frac{1}{- \text{Days left} + 1}, & \text{otherwise}
\end{cases}
}
$$

An easy to understand version of the formula  is shown below:

```js
/**
 * Calculates a priority score for a task based on due date, progress, importance, and effort.
 * @param {string|Date} dueDate - The due date of the task.
 * @param {number} progress - Progress percentage (0 to 100).
 * @param {number} importance - Importance score (1–10).
 * @param {number} xHours - Estimated effort in hours.
 * @returns {number} A priority score; higher values indicate more urgent/important tasks.
 */
function calculatePriority(dueDate, progress, importance, xHours) {
  if (progress === 100) return 0;

  const progressFactor = Math.pow((1 + progress) / 100, 1.6);
  const importanceFactor = Math.pow((1 + importance) / 10, 3);
  const effortFactor = Math.pow(xHours, 1.3);

  const priority = progressFactor * importanceFactor * effortFactor;

  const daysLeft = getDaysLeft(dueDate);

  if (daysLeft > 0) {
    return priority / daysLeft;
  }

  return priority * (-daysLeft + 1);
}

/**
 * Calculates the number of days left until a given due date.
 * 
 * Returns a value that includes today (e.g., if due today, returns 1).
 * If the due date is in the past, the result will be <= 0.
 *
 * @param {string|Date} dueDate - The target due date.
 * @returns {number} Days left until the due date (1-based, can be negative).
 */
function getDaysLeft(dueDate) {
  const today = new Date();
  return 1 + Math.floor((new Date(dueDate) - today) / (1000 * 60 * 60 * 24));
}

```

The formula is somewhat inspired by the Eisenhower matrix. It incorporates the idea of urgency through `Days left` and `Due date`. Importance is represented by `Importance`. The novel part is the integration of the task `Progress`.

I recently came across a similar formula by Tom Gidden (2017), where task priority is calculated as the product of easiness, importance, and urgency. Unlike mine, though, his formula is static and doesn’t adapt over time.

You might notice that I added 1 to many of the parameters (e.g. Importance, Progress). This ensures that the priority never evaluates to 0 when a parameter is 0 unless the task is completed.

## Software

You need a software (e.g. Notion, Excel, …) to do the priority calculations and automatic sorting for you. Since my to-do list is already in Notion, all I had to do was add a Notion formula field to my database and then sort the entries by Priority. The tasks are then tackled in a top-down fashion.

<img src="notion-todo.png" height ="446" width="702" alt = "An example of a to-do list using my formula">
_An example of a to-do list using my formula_

The key takeaways from the above to-do list are:

- If two tasks are otherwise identical, the one already in progress (like Task 2) takes priority over a fresh start (like Task 1).
- An overdue task (like Task 4) ranks higher than another on-time task (Task 3), assuming all other factors are equal.

The Notion formula for calculating the priority is:

```js
prop("Progress") == 100
  ? 0
  : (pow((1 + prop("Progress")) / 100, 1.6) *
      pow((1 + prop("Importance")) / 10, 3) *
      pow(prop("xHours"), 1.3)) /
    (prop("Days left") > 0 ? prop("Days left") : 1 / (-prop("Days left") + 1));
```

Each time you open the to-do list or modify the parameters of a task, Notion automatically re-calculates the priority of the tasks and sorts the to-do list for you.

## General guidelines

In practice, on top of the formula, I also applied the following guidelines:

- I try to avoid working on more than 3 tasks at once.
- Deciding `xHours` can be tricky or nearly impossible for long tasks such as a coding project. In this case, it is essential to break down the task into smaller subtasks which are easier to estimate.
- I manually delete tasks that are past their due dates and have near zero priorities.

## Drawbacks

After 3 years of using my formula on wide range of tasks, including coding related ones, I noticed the following downsides in my formula:

- The scale of the `Importance` parameter is too granular. In practice, I rarely use values like 1, 2, 5, 7, 8 and most of the time I cannot really differentiate between tasks having an importance of $x$ and $x\pm1$. A smaller scale, maybe 1-4, is a much better choice.
- The exponents in the formula are subjective and were tweaked to my liking.
- It relies on the use of digital note taking app with features like sorting and formulas. It is not well-suited for a pen-and-paper approach.

## Conclusion

The formula probably needs some minor adjustments but it has been quite useful for me so far. Like many other priority formulas, my formula is highly subjective and caters specifically for my way of doing things. It is definitely not meant for everyone.

## References

1. Gidden, T., 2017. A Rational Model of Task Priority [online]. Available at: [https://medium.com/@gid/a-rational-model-of-task-priority-16789f50287d](https://medium.com/@gid/a-rational-model-of-task-priority-16789f50287d) [Accessed 15 June 2025].
2. Nutt, W., no date. Notion Formulas: Prioritize Tasks Automatically [online]. Available at: [https://www.notion.vip/insights/notion-formulas-prioritize-tasks-automatically](https://www.notion.vip/insights/notion-formulas-prioritize-tasks-automatically) [Accessed 15 June 2025].