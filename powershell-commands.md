\# PowerShell Commands Used



\## User Creation

net user alice Pass123! /add

net user bob Pass123! /add

net user helpdesk Pass123! /add



\## Group Creation

net localgroup Finance /add

net localgroup ITSupport /add



\## Add Users to Groups

net localgroup Finance alice /add

net localgroup ITSupport helpdesk /add



\## Password Reset

net user alice NewPass123!



\## Disable / Enable Account

net user bob /active:no

net user bob /active:yes



\## Git Commands

git add .

git commit -m "Added lab screenshots"

git push

