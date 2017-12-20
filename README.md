# multithread-server_client
server.c -------------------------------------------
- Initlaized a string array with each index = " offline", index will be my connfd for easy searching , string store in each index is the name of joined user
-getconfd() take a string search which index it locates on the array , return the index else return -1 ;
(didn't bother to change the name )  echo(), this is the primary command handler ,
-if command is list-user , search through the array and print out all of the user name to client except "offline"
-"quit" send "goodbye" to user and return to thread routine , print "user left" and put"offline" to array[confd] .
- @ consist two case , first is broadcast , im message replace @broadcast with array[confd] or the name of the sender the send to all of the confd that is neither "offline" nor " sender" 
. second is private messeage, similar to broadcast with message content handling except receiver will be the @xx use getconfd(@xx) find the confd and send the message to this 

Client .c -------------------------------------
-used two thread one for rx and tx , else program will stuck in one loop (read or write).
-rx and tx is a wrapper for readline and writeline function with fget and fput and some additional conditions for printing out prompt  '>'
-for prompt ">" i print out prompt in one of the thread routine ; after each write;after each Fput; also i had to add some if  and flag for special cases such as list-user because the name comes in line by line with having > in between 
so the format will look like 
> message out
message in
>message out 
message in 
- in rx() if received message "goodby" return to threadrouting and close (fd)

Addtional notes---------------------------
-only support these commands :@user @bradcast list user quit, other command will be ignored 
-for @user and @broadcast , client can sent @xx(space) but not @xx(nothing), the second one will be ignored
-also while you are typing a long message someone send you a message
ex.  >@xx 1234
     @xx hello
     >567
"enter"
on @xx  he will see  >@conley hello
		     @c 1234567
encounterd problems--------------
(1)---
- * temp   array [x] = temp , if i changed things in temp i also changed the thngs in array.
-instead I tried to strcpy string from temp to array (here array is initialized with mallc) i got string segfault , the memory size of the array[] and temp point to are the same , not sure why
- I just avoid copying the string , i just create more independent pointer and make sure i will not be used again 
(2) --
showing correct "> " i most difficult part for me ... I had to do something really weir so i will show up correctly, ugly code .
(3)--
also im not very sure about "support for chating among more than two clients"
I assime it means that the server can take more the 2 client and each client can send message to more the 2 client  
I can also mean like a group with some specific members , which i did not implment this function.
