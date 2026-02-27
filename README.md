# AutomaticEmailSender
#Sends emails automatically for bulk amount of emails at a time.
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.mime.base import MIMEBase
from email import encoders
import os

# ================= USER DETAILS =================
SENDER_EMAIL = "user mail"
APP_PASSWORD = "user paasword"

RECEIVER_EMAILS = [
    "receiver mail"
]

SUBJECT = "Automated Email from Python"
BODY = """
Hello,

This is an automated email sent using Python. 

Thank you.
"""

ATTACHMENT_PATH = "sample.pdf"   # Optional (keep file in same folder)

# ================= EMAIL SETUP =================
try:
    server = smtplib.SMTP("smtp.gmail.com", 587)
    server.starttls()
    server.login(SENDER_EMAIL, APP_PASSWORD)

    for receiver in RECEIVER_EMAILS:
        msg = MIMEMultipart()
        msg["From"] = SENDER_EMAIL
        msg["To"] = receiver
        msg["Subject"] = SUBJECT
        msg.attach(MIMEText(BODY, "plain"))

        # ---------- Attachment ----------
        if os.path.exists(ATTACHMENT_PATH):
            attachment = MIMEBase("application", "octet-stream")
            with open(ATTACHMENT_PATH, "rb") as file:
                attachment.set_payload(file.read())

            encoders.encode_base64(attachment)
            attachment.add_header(
                "Content-Disposition",
                f'attachment; filename="{os.path.basename(ATTACHMENT_PATH)}"'
            )
            msg.attach(attachment)

        server.send_message(msg)
        print(f"Email sent to {receiver}")

    server.quit()
    print("All emails sent successfully!")

except Exception as e:
    print("Error occurred:", e)
