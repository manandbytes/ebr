Eclipse Bundle Recipes
======================

This repositories hosts recipes and tools for building OSGi bundles from Java Maven artifacts.


No bundles to download
----------------------

Due to legal reasons, this project does not offer any OSGi bundles as direct downloads. Please
look at alternate services which may provide ready to use bundles based on these recipies.


How to build all bundles yourself
---------------------------------

1. Clone this repository.
2. `cd recipes`
3. `mvn clean install`

This will publish all OSGi bundles produced by the recipes into your local Maven repository. You can consume
the bundles directly from Maven in any Tycho build. By convention, the bundles will be published using the
Maven group id `org.eclipse.ebr.bundles`.

### Generate p2 repository

1. Switch to the repository root
2. `cd releng/p2`
3. `mvn clean package`

The repository will be made available as archive in `releng/p2/repository/target`.

Note, you **must** build the recipes first and *install* the result into your local Maven repository. Otherwise
the p2 build won't find any bundles.

### How to build just a single recipe?

This is not difficult at all. Just change into the directory of the recipe to build and execute Maven from there.

1. `cd recipes/<path/to/recipe>
2. `mvn clean package`

The resulting bundle will be available in the recipes `target` folder.



How the Maven plug-in works
---------------------------
 1. Direct dependencies are collected an unzipped.
 2. Direct dependencies are then merged with the output produced
    already by the Maven module, i.e. any content in src/main/resources
    or even source code compiled before from src/main/java will take
    precedence over stuff coming from dependencies.
 3. OSGi manifest is generated (via Apache Felix Maven Bundle plug-in).
      - Bundle-SymbolicName is assumed to be the artifact id
        (This is an important assumption made throughout the plug-in
        which can't be changed!)
      - Instructions for BND can be specified via module POM and
        overwrite any default behavior described below
      - The bundle version is inherited from the project version. In
        case the project version ends with "-SNAPSHOT" it will be
        replaced with ".qualifier". Tycho's qualifier expansion is
        supported. Thus, if Tycho computed a proper build qualifier
        then this one will be used as the qualifier replacement.
      - The Bundle-Name is inherited from the Maven project name.
      - The Bundle-Description is inherited from the Maven project
        description.
 4. Final bundle jar is produced.
 5. Source code for dependencies is collected (via sources classifier).
 6. OSGi manifest for source bundle is generated together with
    OSGI-INF/l10n/bundle.properties for localizable-ready content
    (Keys are bundleName and bundleVendor; inherited from main OSGi
    manifest generated before.)
 7. Final source bundle jar is produced.
 8. p2 metadata is generated (via Tycho p2 plug-in).