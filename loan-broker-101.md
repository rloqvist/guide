# Loan broker 101

The loan-brokerage business is fairly simple. There are three types of
actors interaction in the market.

- Consumers - Who are looking to refinance loans
- Brokers - Who help customers `Consumers` compare `Lenders`
- Lenders - Who offer credit to the customer.

Loan brokers aren't needed, per se, but they exist because consumers
don't have the resources (time and effort) to talk to multiple lenders
themselves. The basic process is as follows:

1. A broker approaches a customer through some channel, for example, their website.
2. The broker collects the information necessary to apply for the loan
3. The broker sends the loan to lenders that might be interested.
4. Each lender makes a decision on whether to extend credit or not,
   informing the broker of their decision.
5. The broker presents the offers (if any) to the customer
6. The customer decides on which loan they want.
7. The broker informs the `lender` who has won the customer that their bid is accepted.
8. The lender sends a loan agreement, called a debenture to the
   customer, either digitally or in the mail.
9. The customer signs the debenture.
10. The lender pays out (disburses) the loan to the customer.
11. The `broker` receives a commission if the loan was payed out.

Today the communication between the lenders and brokers is mostly done
through API, because it enables quick, automated decisions. Even so,
some lenders don't offer APIs, in which case they enter their
decisions manually into the broker's system. For lenders who provide
APIs, a number of different approaches are used. Some lenders, usually
those focused on those on high-risk customers, give immediate
responses, with either a rejection or an accept, in the very same API
request. Lower-risk actors often opt for a three outcome model,
automated approval, automated rejection, and manual processing. In the
case of manual processing, the decision is accessible to the broker
through webhooks or polling.
