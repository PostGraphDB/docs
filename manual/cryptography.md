# Cryptography

PostGraph uses the \[PGSodium]\([https://github.com/michelp/pgsodium](https://github.com/michelp/pgsodium)) library to handle it's encryption needs.&#x20;

Function List

randombytes\_random

randombytes\_uniform

randombytes\_buf

crypto\_secretbox\_keygen

crypto\_secretbox\_noncegen

crypto\_secretbox

crypto\_secretbox\_open

crypto\_auth

crypto\_auth\_verify

crypto\_auth\_keygen

crypto\_generichash

crypto\_shorthash(message bytea, key bytea)

crypto\_box\_new\_keypair

crypto\_box\_noncegen

crypto\_box

crypto\_box\_open

crypto\_sign\_keypair

crypto\_sign\_new\_keypair

crypto\_sign

crypto\_sign\_open

crypto\_sign\_detached

crypto\_sign\_verify\_detached

crypto\_pwhash\_saltgen

crypto\_pwhash

crypto\_pwhash\_str

crypto\_pwhash\_str\_verify

crypto\_box\_seal

crypto\_box\_seal\_open
