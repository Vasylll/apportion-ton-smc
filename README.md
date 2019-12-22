The purpose of the smart contract is to apportion (split up) group expenses.
Imagine you are going for a trip with you friends, one friend books and pays for hotel, second friend buys tickets, third spends on fuel to transfer you to the airport and you bouth them all a beer.
Eventually you want to share all expenses and split them all beatween you four.

User stories:
=============
1. I'd like to create new group expenses smc:
crypto/func ../crypto/smartcont/stdlib.fc ../crypto/smartcont/apportion.fc -o../crypto/smartcont/apportion.fif -PS
crypto/fift -s ../crypto/smartcont/new-apportion.fif 0 new-apportion
./lite-client/lite-client -C ton-lite-client-test1.config.json -c 'sendfile new-apportion-query.boc'

2. I'm as a smc owner want to allow my friends (members) to add group expenses and let them know who should pay how much to whom in order to split all group expenses beatween them all.
SMC owner has to know friend's address and public_key:
crypto/fift -s ../crypto/smartcont/apportion-add-member.fif new-apportion 28 kQCvqN4e5M6zdDaQ90M6KGSvXvCoFBCXIGj29X_5JuT7u6qL PuZa0HsTUgfTOCRqnOliTA3LJ6AfUpoDZYrOazrRH/2UHbHf
./lite-client/lite-client -C ton-lite-client-test1.config.json -c 'sendfile apportion-add-member-query.boc'

NOTE, you must add yourself as well to the list of members if you want to be part of group expenses.

3. I'm as a SMC owner want to delete member from the group expense. Member with zero balance is allowed to be deleted only.
SMC owner has to know member_id (numeric representation of member public_key):
crypto/fift -s ../crypto/smartcont/apportion-del-member.fif new-apportion 28 41076509997646347775495477041438463573303009172254142322313646359854482035741
./lite-client/lite-client -C ton-lite-client-test1.config.json -c 'sendfile apportion-del-member-query.boc'

4. I'm as a member of the group expense want to report my expense that should be shared beatweeen the group
The [external] message contains my member_id (numeric representation of member public_key) and it is signed with my privat key.
crypto/fift -s ../crypto/smartcont/apportion-add-expense.fif new-apportion member-keys 41 66.9
./lite-client/lite-client -C ton-lite-client-test1.config.json -c 'sendfile apportion-add-expense-query.boc'

5. I'm as a member of the group expense want to know how much should I pay or how much should I be payed for
./lite-client/lite-client -C ton-lite-client-test1.config.json -c 'runmethod kQCe-R4YcCJpvvYyx3WYeZlhXgvctDAG0SJGPZvghNwVCZ5k get_member_by_id'

6. I'm as a member of the group expense want to pay the amount of grams I owe the group.
Just send amount of grams by simple transfer procedure to the SMC address from the address stored in the member record.
Grams are accumulated on the SMC balance for futher withdrawal.

7. I'm as a member of the group expense want to withdraw the amount of grams I should be payed for (i.e. amount, which I credit the group).
The other member already payed their debt to the SMC address, so there is enought grams on the SMC balance.
The [external] message contains my member_id (numeric representation of member public_key) and it is signed with my privat key.
crypto/fift -s ../crypto/smartcont/apportion-withdraw.fif new-apportion destination-member-keys 51 0.3 destination
./lite-client/lite-client -C ton-lite-client-test1.config.json -c 'sendfile apportion-withdraw-query.boc'

Futher improvements:
====================
- multiple groups support (currently, single group is supported only)
- payer adds expense and specifies members the expense to be split beatween
- each member should either approve/decline the expense (currently, smc owner only approves expense)
- expense is shared beatween members that send approve (currently, expense is shared beatween all members)
- support for other currencies
