# VarBCFiles_testdata

To create a new release

`git clone https://github.com/roelstappers/VarBCFiles_testdata.git`

Make changes to VARBC.cycle files and commit

```julia
using Pkg.Artifacts

# This is the path to the Artifacts.toml we will manipulate
artifact_toml = joinpath(@__DIR__, "Artifacts.toml")

# Query the `Artifacts.toml` file for the hash bound to the name "testdata"
# (returns `nothing` if no such binding exists)
testdata_hash = artifact_hash("testdata", artifact_toml)

# If the name was not bound, or the hash it was bound to does not exist, create it!
if isnothing(testdata_hash) || !artifact_exists(testdata_hash)
    # create_artifact() returns the content-hash of the artifact directory once we're finished creating it
    testdata_hash = create_artifact() do artifact_dir
        # We create the artifact by simply downloading a few files into the new artifact directory
        testdata_url_base= "https://raw.githubusercontent.com/roelstappers/VarBCFiles_testdata/main/" 
        download("$(testdata_url_base)/VARBC.cycle.1", joinpath(artifact_dir, "VARBC.cycle.1"))
        download("$(testdata_url_base)/VARBC.cycle.2", joinpath(artifact_dir, "VARBC.cycle.2"))
        download("$(testdata_url_base)/VARBC.cycle.3", joinpath(artifact_dir, "VARBC.cycle.3"))
    end
    tarball_hash = archive_artifact(hash, "data.tar.gz")
    # Now bind that hash within our `Artifacts.toml`.  `force = true` means that if it already exists,
    # just overwrite with the new content-hash.  Unless the source files change, we do not expect
    # the content hash to change, so this should not cause unnecessary version control churn.
   #  bind_artifact!("Artifacts.toml", "hi", hash, download_info=[("file:/home/steve/dev/Comics/data.tar.gz", tarball_hash)], force=true)
    bind_artifact!(artifact_toml, "testdata", testdata_hash, download_info=[
        ("https://github.com/roelstappers/VarBCFiles_testdata/archive/v1.0.0.tar.gz",tarball_hash)], force=true)
end



```
