**Writing sample (limit 500 words)**: *Describe a time you had to implement a less than ideal workaround solution in order to meet an urgent business need. What was the ideal solution? Why wasn’t it feasible? What did you put in place instead? How did it go?*
===============

At a company that offered a SaaS based diagramming web app, we ran into several vendor-side issues that resulted in revenue loss and a spike in customer service calls.


For context, the original implementation of online payment processing used the iTransact Gateway API. The time delayed and intermittent batch processing of payments created incomplete or duplicate data. These data errors created a steady stream of fraudulent charges which—since the vendor only offered a limited set of credit card fraud detection capabilities—resulted in a spike of customer calls and refund requests. It didn’t help the vendor’s refund process was cumbersome, but regardless, we immediately saw we’d have to move fast to find a better solution.

We saw that a total replacement would be ideal in theory, but untenable to do quickly in practice. To replace the entire existing payment processing vendor, we’d have to migrate hundreds of thousands of existing users from iTransact to Stripe, and test to validate migration, both of which would take more time than we had.

So after some research, we settled on a two-tiered approach with Stripe as the replacement. All new paid customer subscriptions and renewals would be processed through Stripe, while existing customers’ renewals would be processed through iTransact as long as the credit card remained valid.

However, after an existing customer updated their payment information (or if a failed payment for an expired card was detected) they too would begin to have payments processed through the Stripe API as well. So eventually all customers would migrate to Stripe.

This split path choice resulted in near total elimination of fraudulent charges and angry customer calls. While our revenue loss turned into profit and customers stayed loyal to us, we did encounter several issues with legacy customers.

One big issue we discovered was that not all failed payments were gracefully routed through new payment processing flow. In many cases, when an existing customer’s payment was declined, or they updated their payment information, our app behaved as if the expired card was still valid, essentially giving them a free subscription forever. Upon discovery, we triaged and quickly patched the issue, but this leaky-loss of revenue could’ve been avoided if we had had the time to migrate all customers to Stripe at once.
