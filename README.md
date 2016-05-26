# NAME 

Apache::HEADRegistry - Apache::Registry drop-in for HEAD requests

# SYNOPSIS

httpd.conf:

    PerlModule Apache::HEADRegsitry

    <Location /perl-bin>
       SetHandler perl-script
       PerlHandler Apache::HEADRegistry

       Options +ExecCGI
       PerlSendHeader On
    </Location>  

# DESCRIPTION

Apache::HEADRegistry is a drop-in for Apache::Registry that
properly handles HEAD requests.  Currently, Apache::Registry
incorrectly handles HEAD requests - it acts as though they are
GET requests, returning both the headers and content.  So, not
only does represent a way in which mod\_cgi and Apache::Registry
are different, but Apache::Registry is not RFC compliant and
causes trouble with some modern browsers.  This module attempts
to correct the wrong in Apache::Registry by intercepting headers
much in the way that mod\_perl does, but then respecting the value 
of $r->header\_only.

# NOTES

Apache::HEADRegistry is a subclass of Apache::RegistryNG, which
means that it doesn't behave \_exactly\_ the same as Apache::Registry.
Namely, it uses the filename of the script to determine the 
unique package namespace, whereas Apache::Registry uses the URI.
HEADRegistry also does not do any of the auto-dereferencing in its
print() method - if you want that type of thing, then you are
obviously relying on the mod\_perl API and can therefore check
$r->header\_only yourself.  This module is meant for those who
want mod\_cgi emulation only.

# FEATURES/BUGS

The only current bug seems to be for scripts that handle redirects,
such as:

use CGI;
$cgi = CGI->new;
print $cgi->redirect ("http://www.foo.com/");

or 

print "Location: http://www.foo.com/";

What happens is that the default Apache 302 error is displayed
instead of just the headers.  This is a bug both with Apache::Registry
and Apache::HEADRegistry and seems to lie with mod\_perl and it's
internal messing with the $r->assbackwards flag (but I'm not entirely
sure).

This module also does not handle write() calls at the moment - if
you have a need for that let me know.

# SEE ALSO

perl(1), mod\_perl(3), Apache(3), Apache::Registry(3), 
Apache::RegistryNG

# AUTHOR

Geoffrey Young <geoff@modperlcookbook.org>

# COPYRIGHT

Copyright (c) 2002, Geoffrey Young.
All rights reserved.

This module is free software.  It may be used, redistributed
and/or modified under the same terms as Perl itself.

# HISTORY

This code is derived in part from examples in the 
"The mod\_perl Developer's Cookbook"

For more information, visit http://www.modperlcookbook.org/

It also contains code lifted from various mod\_perl internal
sources, such as Apache.pm and mod\_perl.c, and LWP.  Thanks
all for being good open source contributors.
