#!/bin/bash
set -e

RELEASE=1.15.0
JAR_NAME="google-java-format-${RELEASE}-all-deps.jar"
RELEASES_URL=https://repo1.maven.org/maven2/com/google/googlejavaformat/google-java-format
JAR_URL="${RELEASES_URL}/${RELEASE}/${JAR_NAME}"

CACHE_DIR="$HOME/.cache/google-java-format-git-pre-commit-hook"
JAR_FILE="$CACHE_DIR/$JAR_NAME"
JAR_DOWNLOAD_FILE="${JAR_FILE}.tmp"
JAR_PGP_ASC_FILE="${JAR_FILE}.asc"
PGP_SIGN_KEY_ID=EE0CA873074092F806F59B65D364ABAA39A47320

if [[ ! -f "$JAR_FILE" ]]
then
    mkdir -p "$CACHE_DIR"
    curl -L "$JAR_URL" -o "$JAR_DOWNLOAD_FILE"

    # Downloaded from: "${JAR_URL}.asc"
    cat >"$JAR_PGP_ASC_FILE" <<EOF
-----BEGIN PGP SIGNATURE-----

iQIzBAABCgAdFiEE7gyocwdAkvgG9Ztl02SrqjmkcyAFAmImOGEACgkQ02Srqjmk
cyCSXQ//f5cRQNbJhLmHASjRCRFMIe4+dcNd8l+Wy4izsfWctNs+Wj+p/Fnog3y6
J0gY6B/BXB0Y/xZ7xeHhZU2xgWsgCwsTpOyWQw2sCmZV6Bnhvtjw0mxFfuJt1PRk
UMfL8FvalF51rJUBpVcQeRojouccAFnOCMY4KwclWMOwWG1adfjUjrakEbkbHUpB
ohxEYdTvwWkG72A/i0fSS2C0pQybzDWzvv/CweFaIB37cavwOuA2iDYZZC1400it
lofa+zwvpey6CbPZzhESyDycFwriCE5v5txbGmkdNFWMnisDyTnprxVewnm2LHI7
uFenMmT66G7ASNYqbdFWsMtyBhTGRlWWMb0ldxVhZkA6GN2diJHime/iHXR9GS1u
S/uPkbTvp5HnSZjutV6z2X2smN+jJm3GBHNgp+prko8eg5n7wHudh5s616yCzE/L
6ca7en1oH5TbH/9bLLVRNUw5DDlm51wf/pbRuecLYo27LbUjTUKZbhwHK0wV8F0d
vDdno+j2U8KJ4rpifTHJAEnsUcrHvI8siJ7ADnEBnDl9xH+BTKZdS7rKe/vq96LG
NilQ5b4qku7K1VhP1d+/sqXS2UKZmWLWaeJfvI+hj7R619Sn3xNM97sv5c5BYjv8
FPXpXxWWHJ2T0iM4y+yRVRXFyP+owOFCc1jmVXlEgSErZbqHTCA=
=oWka
-----END PGP SIGNATURE-----
EOF

    if ! gpg --list-keys "$PGP_SIGN_KEY_ID"
    then
        gpg --keyserver hkps://keyserver.ubuntu.com --recv-key "$PGP_SIGN_KEY_ID"
    fi
    gpg --verify "$JAR_PGP_ASC_FILE" "$JAR_DOWNLOAD_FILE"
    mv "$JAR_DOWNLOAD_FILE" "$JAR_FILE"
fi

changed_java_files=$(git diff --cached --name-only --diff-filter=ACMR | grep ".*java$" || true)
if [[ -n "$changed_java_files" ]]
then
    echo "Reformatting Java files: $changed_java_files"
    if ! java   --add-exports jdk.compiler/com.sun.tools.javac.api=ALL-UNNAMED \
                --add-exports jdk.compiler/com.sun.tools.javac.file=ALL-UNNAMED \
                --add-exports jdk.compiler/com.sun.tools.javac.parser=ALL-UNNAMED \
                --add-exports jdk.compiler/com.sun.tools.javac.tree=ALL-UNNAMED \
                --add-exports jdk.compiler/com.sun.tools.javac.util=ALL-UNNAMED \
                -jar "$JAR_FILE" --replace --set-exit-if-changed $changed_java_files
    then
        echo "An error occurred, aborting commit!" >&2
        exit 1
    fi
else
    echo "No Java files changes found."
fi
