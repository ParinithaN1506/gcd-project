# Firebase Security Specification - NGOVerse

## Data Invariants
1. **NGOs**: Each NGO must have a unique `ownerId` (the user who created it). Only the owner can update the profile.
2. **Crises**: Anyone signed in can report a crisis. Once reported, only the reporter or an NGO (investigating/active) should be able to update status? Actually, let's say NGOs manage crises.
3. **Updates**: Only an NGO owner can post updates to a crisis.
4. **Donations**: Donors create them. NGOs read them.

## The Dirty Dozen Payloads (Rejection Tests)
1. **Identity Theft**: Update NGO `ownerId` to a different user.
2. **Unverified Spoof**: Create crisis report with `email_verified: false` (if enforced).
3. **Junk ID**: Create doc with 2KB ID string.
4. **Shadow Field**: Add `isAdmin: true` to an NGO document.
5. **Orphaned Update**: Create an `Update` for a non-existent `crisisId`.
6. **Time Travel**: Set `createdAt` to a future date manually.
7. **Price Gouging**: Update a donation `amount` after it's been created.
8. **Status Shortcut**: Move crisis from `reported` directly to `resolved` without being an NGO.
9. **Massive Payload**: Send a 1MB string in a `contact` field.
10. **Cross-User Read**: User A tries to list User B's private donations.
11. **Self-Promotion**: Non-owner tries to update NGO `services`.
12. **Ghost Reporter**: Create crisis with a `reporterId` that doesn't match `auth.uid`.

## Test Plan
Verified via `firestore.rules`.
