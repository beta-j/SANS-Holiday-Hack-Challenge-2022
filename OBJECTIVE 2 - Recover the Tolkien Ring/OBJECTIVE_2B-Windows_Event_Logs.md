# OBJECTIVE 2 - Recover the Tolkien Ring #
## 2B - Windows Event Logs ##

### OBJECTIVE : ###
>Investigate the Windows [event log](Assets/powershell.evtx) mystery in the terminal or offline.  Get hints for this challenge by typing `hint` in the upper panel of the Windows Event Logs terminal.

#  

## PROCEDURE : ##

The terminal provides us with a Windows Powershell log export as a  text file called `powershell.evtx.log`

>**Q1:**	 What month/day/year did the attack take place?  For example, `09/05/2021`

**A1:**	First things first – let’s extract a list of dates found in the log file.  We can use `grep` with a regex expression for this:
```
Cat powershell.evtx.log | grep -o -E “[0-9]{1,2}/[0-9]{1,2}/[0-9]{4}” | uniq | sort
```
The `uniq` and `sort` commands are included to provide unique dates only for the time being and to sort them in order.  We can now grep for each individual date with the `-c` option to see how many times each date occurs in the log file.  By doing this we can tell that the largest amount of log entries where on Christmas eve:  **`12/24/2022`**

 ![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/3f1324c5-e98f-4063-9855-4dfa11bb0fa2)


>**Q2:**	An attacker got a secret from a file.  What was the original file’s name?

**A2:**	By searching for log entries that happened on the 12/24/2022 and that contained the `Add-Content` powershell string we find out that someone replaced `honey` for `fish oil` in a file called **`Recipe.txt`**.
```
Cat powershell.evtx.log | grep 12/24/2022 -B 4 | grep Add-Content
```
![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/f0d27a5c-1cde-4e78-8f51-0a0c18766474)


>**Q3:**	The contents of the previous file were retrieved, changed and stored to a variable by the attacker.  This was done multiple times.  Submit the last full Powershell line that performed only these actions.

**A3:**	From the previous question we can see that the variable used is `$foo`.  We can therefore `grep` for `$foo` and `Get-Content` and see the last time it was assigned a value in the log.  This was on 12/24/2022 at 3:04:37 AM:  **`$foo = Get-Content .\Recipe| % {$_ -replace 'honey', 'fish oil'}`**

>**Q4:**	After storing the altered file contents into the variable, the attacker used the variable to run a separate command that wrote the modified data to a file.  This was done multiple times.  Submit the last full Powershell line that performed only this action.

**A4:**	To answer this we can just `grep` for `$foo` and see when it was last used with `Add-Content`: **`$foo | Add-Content -Path 'Recipe'`**

>**Q5:**	The attacker ran the previous command against a file multiple times.  What is the name of this file?

**A5:**	By now it’s clear that the file in question is **`Recipe.txt`**

>**Q6:**	Were any files deleted? `(Yes/No)`

**A6:**	By grepping for the powershell command `del` we can see that two files where deleted: `recipe_updated.txt` and `Recipe.txt`.  So the answer is **`Yes`**.

>**Q7:**	Was the original file (from question 2) deleted? `(Yes/No)`

**A7:	`No`**

>Q8:	What is the Event ID of the log that shows the actual command line used to delete the file?

**A8:**	By searching for `del .\recipe_updated.txt` by running ``cat powershell.evtx.log | grep 'del \.\\recipe_updated' -B 1`` we find that the associated event ID is **`4104`**

>**Q9:**	Is the secret ingredient compromised `(Yes/No)`?

**A9:**	Clearly this a **`Yes`**

>**Q10:**	What is the secret ingredient?

**A10:**	We know that `Honey` was replaced by `fish oil` – so the secret ingredient must be **`Honey`**
