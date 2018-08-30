# File::ShareDir::Dist [![Build Status](https://secure.travis-ci.org/plicease/File-ShareDir-Dist.png)](http://travis-ci.org/plicease/File-ShareDir-Dist)

Locate per-dist shared files

# SYNOPSIS

    use File::ShareDir::Dist qw( dist_share );
    
    my $dir = dist_share 'Foo-Bar-Baz';

# DESCRIPTION

[File::ShareDir::Dist](https://metacpan.org/pod/File::ShareDir::Dist) finds share directories for distributions.  It is similar to [File::ShareDir](https://metacpan.org/pod/File::ShareDir)
with a few differences:

- Only supports distribution directories.

    It doesn't support perl modules or perl class directories.  I have never really needed anything
    other than a per-dist share directory.

- Doesn't compute filenames.

    Doesn't compute files in the share directory for you.  This is what [File::Spec](https://metacpan.org/pod/File::Spec) or [Path::Tiny](https://metacpan.org/pod/Path::Tiny)
    are for.

- Doesn't support old style shares.

    For some reason there are two types.  I have never seen or needed the older type.

- Hopefully doesn't find the wrong directory.

    It doesn't blindly go finding the first share directory in @INC that matches the dist name.  It actually
    checks to see that it matches the .pm file that goes along with it.

    That does mean that you need to have a .pm that corresponds to your dist name.  This is not
    always the case for some older historical distributions, but it has been the recommended convention
    for quite some time.

- No non-core dependencies.

    [File::ShareDir](https://metacpan.org/pod/File::ShareDir) only has [Class::Inspector](https://metacpan.org/pod/Class::Inspector), but since we are only doing per-dist share
    directories we don't even need that.

    The goal of this project is to have no non-core dependencies for the two most recent production
    versions of Perl.  As of this writing that means Perl 5.26 and 5.24.  In the future, we `may` add
    dependencies on modules that are not part of the Perl core on older Perls.

- Works in your development tree.

    Uses the heuristic, for determining if you are in a development tree, and if so, uses the common
    convention to find the directory named `share`.  If you are using a relative path in `@INC`,
    if the directory `share` is a sibling of that relative entry in `@INC` and if the last element
    in that relative path is `lib`.

    Example, if you have the directory structure:

        lib/Foo/Bar/Baz.pm
        share/data

    and you invoke perl with

        % perl -Ilib -MFoo::Bar::Baz -MFile::ShareDir::Dist=dist_share -E 'say dist_share("Foo-Bar-Baz")'

    `dist_share` will return the (absolute) path to ./share/data.  If you invoked it with:

        % export PERL5LIB `pwd`/lib
        perl -MFoo::Bar::Baz -MFile::ShareDir::Dist=dist_share -E 'say dist_share("Foo-Bar-Baz")'

    it would not.  For me this covers most of my needs when developing a Perl module with a share
    directory.

    [prove](https://metacpan.org/pod/prove) foils this heuristic by making `@INC` absolute paths.  To get around that you can use
    [App::Prove::Plugin::ShareDirDist](https://metacpan.org/pod/App::Prove::Plugin::ShareDirDist).

- Built in override.

    The hash `%File::ShareDir::Dist::over` can be used to override what `dist_share` returns.
    You can also override behavior on the command line using a dash followed by a key value pair
    joined by the equal sign.  In other words:

        % perl -MFile::ShareDir::Dist=-Foo-Bar-Baz=./share -E 'say File::ShareDir::Dist::dist_share("Foo-Bar-Baz")'
        /.../share

    If neither of those work then you can set PERL\_FILE\_SHAREDIR\_DIST to a dist name, directory pair

        % env PERL_FILE_SHAREDIR_DIST=Foo-Bar-Baz=`pwd`/share perl -MFile::ShareDir::Dist -E 'say File::ShareDir::Dist::dist_share("Foo-Bar-Baz")'

    For [File::ShareDir](https://metacpan.org/pod/File::ShareDir) you have to either mock the `dist_dir` function or install
    [File::ShareDir::Override](https://metacpan.org/pod/File::ShareDir::Override).  For testing you can use [Test::File::ShareDir](https://metacpan.org/pod/Test::File::ShareDir).  I have never
    understood why such a simple concept needs three modules to do all of this.

# FUNCTIONS

Functions must be explicitly exported.  They are not exported by default.

## dist\_share

    my $dir = dist_share $dist_name;
    my $dir = dist_share $module_name;

Returns the absolute path to the share directory of the given distribution.

As a convenience you can also use the "main" module name associated with the
distribution.  That means if you want the share directory for the dist
`Foo-Bar-Baz` you may use either `Foo-Bar-Baz` or `Foo::Bar::Baz` to find
it.

Returns nothing if no share directory could be found.

# ENVIRONMENT

- PERL\_FILE\_SHAREDIR\_DIST

    Can be used to set a single dist directory override.

# CAVEATS

All the stuff that is in [File::ShareDir](https://metacpan.org/pod/File::ShareDir) but not in this module could be considered either
caveats or features depending on your perspective I suppose.

# SEE ALSO

- [File::ShareDir](https://metacpan.org/pod/File::ShareDir)

# AUTHOR

Author: Graham Ollis <plicease@cpan.org>

Contributors:

Yanick Champoux (yanick)

# COPYRIGHT AND LICENSE

This software is copyright (c) 2017,2018 by Graham Ollis.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
