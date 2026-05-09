# Fit assessment

Use this prompt when the user wants a fit check on a role without producing application materials. It is the same fit assessment that runs as Step 1 of `apply.md`, extracted so the user can run it standalone.

If the recommendation is "do not apply" because of a hard dealbreaker, the user can stop here and save themselves the time of running the full apply prompt.

This prompt and `apply.md` Step 1 must produce the same fit assessment for the same input. If you change one, change the other.

## What the user must give you

1. The user's `profile/` folder, in particular `profile.md` (for evidence) and `filter.md` (for dealbreakers and seniority range). `voice.md`, `format.md`, and `cover-letter.md` are not needed for this prompt.
2. The job description.

## What you do

Compare the JD against the user's `target_roles` (in profile.md), the role evidence in profile.md, and the dealbreakers in filter.md.

Output the fit assessment in this exact structure (identical to apply.md Step 1):

```
## Fit assessment

**Overall fit:** Strong / Reasonable / Weak / Do not apply

**Match against filter:**
- [Each item from filter.md that this role passes or fails. Be specific. If a dealbreaker is present, name it.]

**Match against profile:**
- [The 3-5 strongest pieces of evidence from the profile that line up with the JD's requirements. Reference role IDs and achievement IDs where useful.]

**Real gaps:**
- [The 2-4 things the role asks for that are genuinely missing or thin. Do not soften these.]

**Recommendation:** [One short paragraph. Apply, apply but with eyes open, or do not apply.]
```

## Rules

- Be honest. The point is to save the user from wasted applications.
- If a hard dealbreaker from the "Dealbreakers (hard rules)" section of filter.md is present in the JD or the company, your recommendation must be "do not apply." No workarounds.
- If the role is more than two seniority levels above what the profile supports, say so. Do not stretch.
- If the role is below the user's seniority floor, say so. Do not assume flexibility.
- Gaps are gaps. Do not paper over them.

## Things you must not do

1. Do not produce a CV, cover letter, or selection JSON. This prompt is fit assessment only. Direct the user to `apply/apply.md` if they want documents.
2. Do not soften the recommendation to be polite. The user will not thank you for letting them apply for the wrong role.
3. Do not invent evidence. Use only what is in profile.md.
4. Do not echo the JD's language back.

## After the assessment

End with one short note:

> If you want to proceed, run `apply/apply.md` next with the same files. It re-runs this assessment and then produces the application JSON.
>
> If the recommendation was "do not apply" because of a hard dealbreaker, no need to run apply.md. Save your time.
>
> If you disagree with the recommendation, tell me what I missed. Sometimes a JD reads weaker than the role actually is.
