# Video script (target: 2 min 30 s, hard cap 3 min)

Keep the diagram (docs/architecture.png) on screen the whole time. Talk over it; don't open code.

## 0:00 – 0:20  What it is
"This is pre-req-vault, an Anchor program on Solana. Every wallet gets its own vault where it
can deposit and withdraw SOL. Programs on Solana are stateless, so all the state lives in
accounts, and here those accounts are PDAs: addresses derived from seeds that only the program
can sign for."

## 0:20 – 0:55  The two PDAs (point to the green boxes)
"There are two accounts per user. VaultState is a small data account derived from the user's
public key. It just stores two bump bytes, so later instructions can re-derive the PDAs without
searching. The Vault itself is a plain System account derived from VaultState. It holds no data,
only lamports. Nobody has a private key for it. When the program needs to move SOL out, it
signs with the seeds instead."

## 0:55 – 1:40  The four instructions (point to the blue column, top to bottom)
"Initialize creates VaultState and records the bumps. Deposit is a CPI to the System program
moving SOL from the user to the vault; the user is the signer, so nothing special is needed.
Withdraw is the interesting one. First it transfers SOL from the vault back to the user, and
because the vault is a PDA, the program signs that transfer with the vault seeds. Then it makes a
second CPI, this time into Turbin3's registration program, calling its initialize instruction
with my GitHub username. That creates the ApplicationAccount, which is owned by the registration
program, not by mine. Close drains the vault and closes VaultState so the rent comes back."

## 1:40 – 2:10  Why the CPI design works (point to the orange box and dashed arrow)
"Two things make the CPI safe. My program never trusts the client for the registration
program's address: the account is typed against the program ID from the IDL. And the
application account's seeds are checked against that program too, so you can't pass in a
random account. The user signed the outer transaction, so that signature flows through to the
callee, which sees the user as signer and payer. Both steps are in one transaction, so if
registration fails, the withdrawal is rolled back."

## 2:10 – 2:35  State over time (point to the bottom strip)
"Across the test suite: after initialize, VaultState exists and the vault is empty. After
deposit, the vault holds one SOL. After withdraw, it holds half a SOL and the registration
record now exists with my GitHub handle. After close, everything of mine is gone and the rent is
refunded, but the registration record stays, because it belongs to the other program."

## 2:35 – 2:50  Wrap
"The program is deployed on devnet under my own program ID, and the withdraw transaction in the
README shows the nested invocation in the logs. That's the whole flow."

Tips: upload to YouTube as Unlisted, then open Subtitles and click "auto-generate" (or upload
this script as the transcript) so captions are available before you submit.
