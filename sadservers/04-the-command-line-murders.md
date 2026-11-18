# Incident Report: The Command Line Murders

**Platform:** SadServers  
**Type:** Do  
**Level:** Easy  
**Domain:** Linux System Administration / Text Processing & Investigation

## The Scenario

A murder mystery solved entirely with shell tools. The clues are in
`/home/admin/clmystery/mystery`, and the murderer's name goes into `~/mysolution`.

## Investigation & Fix

1. **Found the real clues.** `crimescene` is full of Alice in Wonderland filler,
   so `grep -i "murder"` was useless. `grep "CLUE" crimescene` gave three clues:

   - The killer is a male, at least 6' tall.
   - The wallet held cards for Rotary_Club, Delta SkyMiles, the library, and the
     Museum of Bash History.
   - A witness named Annabel has blond spiky hair and a NZ accent.

2. **Located the witness.** `grep Annabel people` gave four people, and two are
   female (Annabel Sun and Annabel Church). I looked up each address with
   `sed -n 'LINEp' streets/STREET`, which pointed to an interview number.

3. **Read the interviews.** Sun was ruled out (brown hair, not from NZ). Church
   saw the getaway car: a blue Honda, plate `L337…9`.

4. **Intersected the memberships** to find people in all four clubs:

   ```bash
   grep -Fxf memberships/Rotary_Club memberships/Museum_of_Bash_History \
    | grep -Fxf - memberships/Delta_SkyMiles \
    | grep -Fxf - memberships/Terminal_City_Library > ~/members.txt
   ```

   That left 25 names.

5. **Joined with the vehicles file** to find blue Honda owners with a matching
   plate who are also in `members.txt`:

   ```bash
   grep -A5 "^License Plate L337..9$" vehicles | grep -A3 "Make: Honda" \
    | grep -A2 "Color: Blue" | grep "Owner:" | sed 's/Owner: //' \
    | grep -Fxf - ~/members.txt
   ```

   Only one name came out: **Joe Germuska**.

6. **Verified and submitted.** I confirmed gender and height in `people` and
   `vehicles`, wrote the name to `~/mysolution`, and checked the `md5sum`.

## Lesson

Treat every clue as a filter, and chain `grep` stages so only matching records
survive. `grep -Fxf fileA fileB` prints the lines of `fileB` that also appear in
`fileA`, which makes it a handy way to intersect two lists.
