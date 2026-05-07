# **Case Study: Resolve System-Level Risk Through Edge-Case Testing**

## Table of Contents

1. [Overview](#1-overview)
2. [Turning Save Point Safety into a Reliable Gameplay Rule](#2-turning-save-point-safety-into-a-reliable-gameplay-rule)
3. [Resolving a Crash from Unexpected Player Behavior](#3-resolving-crash-from-unexpected-player-behavior)
4. [Alerting a Data Modeling Issue with User Identity](#4-alerting-data-modeling-issue-with-user-identity)
5. [Preventing Revenue Loss from Loose Authorization Logic](#5-preventing-company-revenue-loss-from-loose-authorization-logic)

## 1. Overview

Across gameplay systems and education platform features, I focused on identifying and improving areas where the software depended on hidden assumptions or expected user behavior.

To analyze issues from a system-level perspective, I used the following workflow:
1. Identify assumptions.
2. Design an edge-case test.
3. Reproduce the failure.
4. Analyze the root cause.
5. Search for system-level risks.
6. Communicate with PM/engineering team.
7. Help the team address the root issue and resolve.

Using this approach I found failures caused by hidden system assumptions, including issues that could affect product reliability and revenue.

**The main areas I tested were:**

- Gameplay state consistency
- Save and progression logic
- Account data integrity
- User authorization
- Paid or assigned content access

## 2. Turning save point safety into a reliable gameplay rule

### Situation

Players expect save points to be safe areas where they can save progress, rest, and open upgrade menus.

During normal play, most save points appeared safe. However, I wanted to verify whether that safety was actually guaranteed across all levels, especially in areas where enemies could be pulled from nearby spawn or patrol zones.

### Test Approach

I tested save points across all levels where enemies could be pulled from nearby spawn or patrol areas.

My test flow was:
1. Trigger enemy.
2. Keep enough distance for the enemy to follow.
3. Lead the enemy back to the save point.
4. Open a save point menu.
5. Check whether the player could still be attacked.

### Result and Analysis

Some save points had safety collision volumes that protected the player correctly, while others allowed enemies to enter and attack during menu interaction. 

Using Unreal Editor, I confirmed that safety collision volumes were applied per save point. This explained the inconsistency: the safety rule depended on manual setup at each location.

I reported the issue with reproduction steps, screenshots, video evidence, and an explanation of why it should be treated as a critical and urgent consistency issue. The main risk was that players could lose trust in a repeated gameplay rule. If the game teaches players that save points are safe, every save point needs to uphold that rule.

### Impact

The team applied safety collision volumes across the save points, making the behavior consistent across levels before the official launch.

This taught me the importance of reusable components and shared assets for enforcing uniform behavior across levels.

## 3. Resolving crash from unexpected player behavior

### Situation

In this gameplay flow, a save point was placed before a boss fight. The expected path was simple: the player reaches the save point, saves progress, enters the boss fight, clears the boss, and continues.

I wanted to test what would happen if the player skipped that save point before entering the boss fight.

### Test Approach

The reproduction flow was:
1. Approach the pre-boss save point.
2. Jump over it without triggering the save point collision.
3. Enter the boss fight.
4. Clear the boss.
5. Return to the same save point after the fight to continue.

### Result and Analysis

The game crashed immediately when the player returned to the save point. The crash was 100% reproducible.

My analysis was that the game expected the pre-boss save trigger to update progression state before the boss was cleared. Because the player could skip the trigger, the game entered a state where boss progression and save progression were no longer aligned.

The team fixed the issue by increasing the save collision volume so the save trigger would activate even if the player jumped over the save point.

### Impact

This report led to the resolution of a 100% reproducible client crash caused by unexpected player behavior. Also, by fixing the size of the collision only, the problem could be fixed with a low risk level-design adjustment. No code-level change was required.

I learned that the system can fail when the it assumes players will follow the intended path. Thinking outside of box is fun, and it also gives chances to improve the product reliability.

## 4. Alerting data modeling issue with user identity

### Situation

Most account-based systems use email addresses for notifications, billing, account updates, password recovery, or identity verification. In my opinion it is crucial part of engineering how to set a relationship with user account and email address.

After confirming that an email address could be changed successfully in the account settings UI, I wanted to see whether the system prevented another account from using the same email address.

### Test Approach

I logged in with a second account and changed its email address to one already used by another account.

### Result and Analysis

As a result, multiple accounts could share the same email address. Allowing multiple accounts to share the same email address without a clear product rule could create ambiguous ownership and additional complexity in authentication, billing, support, and development.

The questions I raised were:

- Which account owns the email address?
- Which account should receive account-related notifications?
- What happens if password recovery depends on email?
- Could billing or authorization logic behave incorrectly?
- Could support or admin workflows confuse one account with another?

I escalated the issue to the PM and engineering team as a specification and data integrity problem, not just a UI validation bug. My recommendation was that the product needed a clear rule for the relationship between accounts and email addresses. Unless shared emails were intentional, the system should enforce a one-to-one relationship between account identity and email to ensure its security.

### Impact

Development around the related function was paused for review. I moved off the project before the final implementation was completed, so I do not want to overstate the final result. The confirmed impact was that the issue was identified early and treated as a product/data-modeling concern rather than a minor validation defect.

Profile fields are core identifiers. If a field is used across authentication, communication, billing, or authorization, it needs clear design and constraints during the data-modeling process.

## 5. Preventing company revenue loss from loose authorization logic

### Situation

Users could access digital contents in both offline and online modes. Regardless of where the content storage is, user goes through authentication process to access digital content.

The area I wanted to verify was locally downloaded content. The content is executed through via Unreal client, so the system needed to verify that the current user was authorized to access the downloaded content.

### Test Approach

I tested this with two accounts on the same machine.

First, I logged in with Account A, which had access to specific content. I downloaded the content and confirmed that it was stored locally. The client executed the content normally. Then I logged out and logged in with Account B, which did not have permission for that content. I tried to access to that content with Account B.

### Result and Analysis

Account B was still able to see and use the content it should not have been allowed to access. 

This suggested that the client was using local file presence as part of the available content list instead of relying only on the current user’s authorization state.

The risk was serious because content access was tied to payment status or organization-level contracts. If one authorized user downloaded content on a shared device, another unauthorized user could potentially access it without purchasing it or being granted permission.

I reported the issue to the team and explained that content visibility and access should be based on the current user’s authorization state. Local caching can improve performance, but it should not become the source of truth for permissions.

As an immediate direction, I recommended that unauthorized content should not appear in the current user’s content list. Longer term, the system needed stronger entitlement validation so cached content could not bypass access rules.

### Impact

I identified a potential revenue-loss risk before launch and helped the team address an authorization issue.
