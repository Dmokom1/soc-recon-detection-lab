# Personal Build Notes

## Observation
Initial scan generated multiple Suricata alerts as expected.

## Challenge
Too many alerts made it hard to immediately identify attacker behavior.

## Solution
Filtered by source IP and dataset in Kibana.

## Detection Strategy
Created threshold rule to group repeated alert activity.

## Lesson Learned
Raw alerts are noisy. Value comes from filtering, correlation, and detection logic.