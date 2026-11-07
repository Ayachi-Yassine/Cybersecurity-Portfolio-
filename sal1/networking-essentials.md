# Networking Essentials

DNS records

A record: The A (Address) record maps a hostname to one or more IPv4 addresses. For example, you can set [example.com](http://example.com/) to resolve to 172.17.2.172.
AAAA Record: The AAAA record is similar to the A Record, but it is for IPv6. Remember that it is AAAA (quad-A), as AA and AAA would refer to a battery size; furthermore, AAA refers to Authentication, Authorization, and Accounting; neither falls under DNS.
CNAME Record: The CNAME (Canonical Name) record maps a domain name to another domain name. For example, [www.example.com](http://www.example.com/) can be mapped to [example.com](http://example.com/) or even to [example.org](http://example.org/).
MX Record: The MX (Mail Exchange) record specifies the mail server responsible for handling emails for a domain.

Example commands defined by the FTP (21) protocol are:

USER is used to input the username
PASS is used to enter the password
RETR (retrieve) is used to download a file from the FTP server to the client.
STOR (store) is used to upload a file from the client to the FTP server.

Let’s present some of the commands used by your mail client when it transfers an email to an SMTP (25)server:

HELO or EHLO initiates an SMTP session
MAIL FROM specifies the sender’s email address
RCPT TO specifies the recipient’s email address
DATA indicates that the client will begin sending the content of the email message
. is sent on a line by itself to indicate the end of the email message

Without going into in-depth technical details, an email client sends its messages by relying on SMTP and retrieves them using POP3. SMTP is similar to handing your envelope or package to the post office, and POP3 is similar to checking your local mailbox for new letters or packages.

Some common POP3 (110) commands are:

USER <username> identifies the user
PASS <password> provides the user’s password
STAT requests the number of messages and total size
LIST lists all messages and their sizes
RETR <message_number> retrieves the specified message
DELE <message_number> marks a message for deletion
QUIT ends the POP3 session applying changes, such as deletions

protocol that allows synchronization of messages instead of deleting a message after retrieving it. One solution to maintaining a synchronized mailbox across multiple devices is Internet Message Access Protocol (IMAP).

The IMAP (143)protocol commands are more complicated than the POP3 protocol commands. We list a few examples below:

LOGIN <username> <password> authenticates the user
SELECT <mailbox> selects the mailbox folder to work with
FETCH <mail_number> <data_item_name> Example fetch 3 body[] to fetch message number 3, header and body.
MOVE <sequence_set> <mailbox> moves the specified messages to another mailbox
COPY <sequence_set> <data_item_name> copies the specified messages to another mailbox
LOGOUT logs out

Protocol	Transport Protocol	Default Port Number
TELNET	TCP	23
DNS	UDP or TCP	53
HTTP	TCP	80
HTTPS	TCP	443
FTP	TCP	21
SMTP	TCP	25
POP3	TCP	110
IMAP	TCP	143

### HTTP Over TLS

# 

HTTPS stands for Hypertext Transfer Protocol Secure. It is basically HTTP over TLS. Consequently, requesting a page over HTTPS will require the following three steps (after resolving the domain name):

1. Establish a TCP three-way handshake with the target server
2. Establish a TLS session
3. Communicate using the HTTP protocol; for example, issue HTTP requests, such as `GET / HTTP/1.1`

| **Protocol** | **Default Port Number** |
| --- | --- |
| HTTP | 80 |
| SMTP | 25 |
| POP3 | 110 |
| IMAP | 143 |

The secure versions, i.e., over TLS, use the following TCP port numbers by default:

| **Protocol** | **Default Port Number** |
| --- | --- |
| HTTPS | 443 |
| SMTPS | 465 and 587 |
| POP3S | 995 |
| IMAPS | 993 |