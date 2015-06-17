BUILD
=====

Currently we hardcode /cvs/Urho3D as well as hardcoding URHO3D_OPENGL,
and the reality is that we should instead install Urho3D into a
prefix, and use pkg-config to fetch the flags for the install (so we
actually get the configured flags correctly, and we do not need to
hardcode URHO3D_OPENGL everywhere)

This is worng on window.cs

                new void RegisterObject (Context context)
                {
                        Window_RegisterObject (context == null ? IntPtr.Zero : context.handle);
                }


Build
-----
PhysicsWorld and RigidBody are pending, since they surface some bt data types
that I do not currently bind.

Events
======

Object.cs/ObjectCallback: do we even need the stringHash in the event?

Seems like we do not, since we proxy everything.

Binding
-------
Urho3D::String(string) would currently call a constructor that assumes
that the value woudl not be released, we probably need to change this
constructor to call instead String(string,length) so that it makes
its own copy

Structures
----------

Should fix code generation for structures, they should likely use "ref" to initialize themselves?

Generator
---------

Serializer, Deserializer
========================
They are only used in a couple of places as base classes, and the type
is in general not used to pass data around.   So we should inline the
methods from those classes in the classes that adopt them.

(File, MemoryBuffer, VectorBuffer)

String&
=======

Return values can be the new StringPtr data type, which is a wrapper
around the pointer and is implicitly convertible to string.

For calling, we need to figure out what to do here.   We could create
an on-stack String on demand based on a C# string for an overload method
and always surface the one that takes a "ref String" method.

Enumerations
============

They currently just dump the name, we need to dump the value

We should also likely dump the values as constants, and use the
constants inside real enumeration definitions (because trying to
prettify this automatically is a lot of work for little gain,
85 or so enumerations)

PInvoke generator
=================

Needs to generate unique names for the C method names, currently, we
have clashing names like these for the C functions:

String_String ()
String_String (const char *)

So for P/Invoek we need unique signatures

Rename/Remap some types
=======================
String is a bad name, because it is easy to clash, this type should 
likely be renamed to UrhoString