.. title: I saw a little elf, let's catch him
.. slug: i-saw-a-little-elf-lets-catch-him
.. date: 2017-08-27 17:02:03 UTC
.. tags: elf, python
.. category: tech, fun
.. link: 
.. description: 
.. type: text


It was a bored weekend, untill Mr. Nobody, (a friend LOL) gave a challenge which he was unable to figure out.
It was posted at `ringzer0team.com` website, which is popular for such challengers. The challenge was to get
a secret word out of a huge payload which seems to be encoded. The name of the challenge is "I saw a little elf".

.. image:: /galleries/i-saw-a-little-elf/elf-meme.jpg

This is going to be a long post, just hold your breath. This is how I figured it out. First, I did a google search (I'm smart), and yes, some people have already solved it, however the solutions have less information on what the problem really is. Then I started to understand the problem with my own. Here's the problem. (I have removed 90% of message body to make it small and clear)

* URL : https://ringzer0team.com/challenges/15

.. code-block:: shell

		You have 3 seconds to get the secret word.
   Send the answer back using https://ringzer0team.com/challenges/15/[your_string]

		----- BEGIN Elf Message -----
   UTJkQ01HRlhOWEJZZDBFeFRHcEpkVTFzT1VSUmEyeE5VakJDUVdOSFZteGlTRTFCV2xkNGFWbFdVbXhpYlRselVUQXhWV050VmpCak1teHVXbGhLWmxSV1VrcFlkMEptV0RCU1QxSldPVVJVVmxKbVdIZENlbHB........etc

		----- End Elf Message -----

		----- BEGIN Checksum -----
		44fde7bfd0a94e2352f4616bd0670ffd
		----- END Checksum -----

		
1. Figure out the encoded method.

The string length was 37164, but when I refresh, it gives another string with a different length. However,  they have some similar attributes, such as the lengths are multiple of 4 and every character is in the set of [ A-Z, a-z, 0-9, +, / ], except the padding at the end. So the conclusion is, the encoded method should be base64. But it didn't give a useful
message when I decoded the string. Why?

2. Why they have given a checksum ?

The checksum is used to verify a file, message or anything related to hashing. The checksum has 32 characters, which is md5.
Then, I used md5 to generate a hash for the encoded value, and it gave something different. The expected value would be the same as the checksum. I tried a few times, and suddenly it gave the expected result. I ran it again but no luck this time. So what I was missing ? The iteration. I call the script in a loop until I get what I needed, and it gave the result several times, but not always. Now I have the answer for the 1st step. When the checksum matches, the encoded string gets something meaning full.

3. Elf files

Now I have the message, but what is it ? yes, it's an elf file, which stands for **Executable and Linking File**. Great, I wrote the string to a file and used `readelf` command to read it (I wrote as a byte string). It showed the complete details of the file. But I'm yet to figure out the hidden message.

.. code-block:: shell

   readelf -a myelf.elf

4. Execute Elf file

I renamed my elf file to .out file. Then changed the permissions and executed. Whoa, it printed the answer which I was looking for. :)

.. code-block:: shell

   chmod +x myelf.out && ./myelf.out

5. The Time

Time is the common enemy. It gives 3 seconds to catch the Elf. When I write an elf file and execute, 90% of the attempts it failed to accomplish the task within the given time. Instead of executing the file, I opened it and search for the answer (in this case I printed the answer), and it was there, but not that easy to read, because it was in a byte string.
   
6. Vim to rescue

In vim, you can get the details of your current position in the buffer by using **g ctrl+g** in command mode.(other text editors might have similar methods). So, I was able to get the exact location of the hidden word. Now, in the script, without even writing the byte string to a file, I was able to capture the answer by defining a character range. In this method, it has almost 100% accuracy on providing the correct answer on time.

Code junk : https://github.com/jantwisted/wwwcraw-elf

Happy Hacking ! :)
