# Creating a PGP key for Releases

On a Mac:

    brew install gpg

How to create a key: http://www.apache.org/dev/openpgp.html#generate-key
 * email = you@apache.org
 * description = CODE SIGNING KEY

Look at your keyring:

    gpg --list-sigs --fingerprint

Example Output:

    pub   4096R/8A496450 2014-02-27
          Key fingerprint = B998 A96C 4DAA 821A 9C3A  FA5C E28E 332A 8A49 6450
    uid                  Andrew Grieve (CODE SIGNING KEY) <agrieve@apache.org>
    sig 3        8A496450 2014-02-27  Andrew Grieve (CODE SIGNING KEY) <agrieve@apache.org>
    sub   4096R/A59029E7 2014-02-27
    sig          8A496450 2014-02-27  Andrew Grieve (CODE SIGNING KEY) <agrieve@apache.org>

In this example, `8A496450` is your `$KEY_ID`


Publish to `cordova-coho/KEYS`:

    cd cordova-coho
    gpg --armor --export $KEY_ID >> KEYS
    git commit -am "Added my signing PGP key to KEYS"
    git show # Verify
    git push

Then copy the update KEYS to `dist/`:

    # Clone if you don't have it already:
    ./cordova-coho repo-clone -r dist
    cp cordova-coho/KEYS cordova-dist/KEYS
    # Make sure that's the only change to the file
    ( cd cordova-dist && svn diff )
    # Commit
    ( cd cordova-dist && svn commit )

Publish to `keys.gnupg.net`

    gpg --send-keys $KEY_ID

Visit http://pgp.mit.edu/ and paste in result of:

    gpg --armor --export $KEY_ID | pbcopy

Sign into: https://id.apache.org/ and add your fingerprint (not your KEY_ID):

Copy to your Apache homedir:

    gpg --armor --export $KEY_ID > $KEY_ID.asc
    scp $KEY_ID.asc people.apache.org:

Phew! That was easy :P


# Importing PMC Members' PGP keys

    curl "https://people.apache.org/keys/group/cordova-pmc.asc" | gpg --import

Import any extra committer's keys via:

    curl "https://git-wip-us.apache.org/repos/asf?p=cordova-coho.git;a=blob_plain;f=KEYS;hb=master" | gpg --import

