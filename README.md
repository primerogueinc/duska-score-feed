# Proofs

Written by .github/workflows/stamp.yml. One manifest and one OpenTimestamps proof for every commit on main of primerogueinc/duska-score-feed.

Check one: ots verify proofs/NAME.manifest.ots (pip install opentimestamps-client), or drop the manifest and its .ots file on opentimestamps.org.
Check a manifest against the repo: git checkout the commit it names, then sha256sum any file and compare with its line.
