import os
import requests
import smtplib
from datetime import date, timedelta
from email.mime.text import MIMEText

# HORSE TO TRACK
HORSE_NAME = "Zedan"

# EMAIL TO RECEIVE UPDATES
SEND_TO = "danielokeefe475@gmail.com"

# Racing API credentials
API_USERNAME = os.getenv("RACING_API_USERNAME")
API_PASSWORD = os.getenv("RACING_API_PASSWORD")

# Gmail credentials
EMAIL_USER = os.getenv("EMAIL_USER")
EMAIL_PASS = os.getenv("EMAIL_PASS")


def get_recent_results(horse_name):
    url = "https://api.theracingapi.com/v1/results/search"

    params = {
        "horse": horse_name,
        "start_date": str(date.today() - timedelta(days=30)),
        "end_date": str(date.today())
    }

    response = requests.get(
        url,
        params=params,
        auth=(API_USERNAME, API_PASSWORD),
        timeout=20
    )

    if response.status_code != 200:
        print("Error fetching race data")
        return []

    data = response.json()

    return data.get("results", [])


def build_email(horse_name, results):

    if not results:
        body = (
            f"No new race results or updates found for "
            f"{horse_name} this month."
        )

    else:
        lines = [
            f"Monthly Racing Update for {horse_name}",
            "",
            "Recent Results:",
            ""
        ]

        for r in results:

            lines.append(
                f"Date: {r.get('date', 'N/A')}"
            )

            lines.append(
                f"Track: {r.get('course', 'N/A')}"
            )

            lines.append(
                f"Race: {r.get('race_name', 'N/A')}"
            )

            lines.append(
                f"Finish Position: {r.get('position', 'N/A')}"
            )

            lines.append(
                f"Jockey: {r.get('jockey', 'N/A')}"
            )

            lines.append(
                f"Trainer: {r.get('trainer', 'N/A')}"
            )

            lines.append("------------------------")

        body = "\n".join(lines)

    return MIMEText(body)


def send_email(subject, message):

    message["Subject"] = subject
    message["From"] = EMAIL_USER
    message["To"] = SEND_TO

    with smtplib.SMTP_SSL("smtp.gmail.com", 465) as server:

        server.login(EMAIL_USER, EMAIL_PASS)

        server.send_message(message)

        print("Monthly horse update email sent successfully.")


def main():

    results = get_recent_results(HORSE_NAME)

    email_message = build_email(HORSE_NAME, results)

    send_email(
        f"Monthly Horse Update: {HORSE_NAME}",
        email_message
    )


if __name__ == "__main__":
    main()
