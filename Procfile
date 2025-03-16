from flask import Flask, send_file, request
import io
import random
from datetime import datetime
import requests
from PyPDF2 import PdfReader, PdfWriter

app = Flask(__name__)

# Dictionary mapping PDF identifiers to their remote URLs
PDF_URLS = {
    "pdf1": "https://example.com/path/to/pdf1.pdf",
    "pdf2": "https://example.com/path/to/pdf2.pdf",
    "pdf3": "https://example.com/path/to/pdf3.pdf",
}

def fetch_remote_pdf(url):
    """Fetch a remote PDF and return it as a BytesIO object."""
    response = requests.get(url)
    return io.BytesIO(response.content)

def add_watermark_to_pdf(input_pdf, watermark_text):
    """Add a watermark to each page of the PDF."""
    reader = PdfReader(input_pdf)
    writer = PdfWriter()

    for page in reader.pages:
        # Create a watermark PDF
        watermark_buffer = io.BytesIO()
        c = canvas.Canvas(watermark_buffer, pagesize=letter)
        c.setFont("Helvetica-Bold", 12)
        c.setFillColor(red)  # Red text
        c.setStrokeColor(green)  # Green border
        c.drawString(50, 50, watermark_text)  # Position the watermark
        c.save()

        # Merge the watermark with the current page
        watermark_buffer.seek(0)
        watermark_reader = PdfReader(watermark_buffer)
        watermark_page = watermark_reader.pages[0]
        page.merge_page(watermark_page)
        writer.add_page(page)

    # Save the watermarked PDF to a buffer
    output_buffer = io.BytesIO()
    writer.write(output_buffer)
    output_buffer.seek(0)
    return output_buffer

@app.route("/download-pdf")
def download_pdf():
    # Get the PDF identifier from the query parameter
    pdf_id = request.args.get("id")
    if not pdf_id or pdf_id not in PDF_URLS:
        return "Invalid PDF ID", 400

    # Fetch the remote PDF
    remote_pdf_url = PDF_URLS[pdf_id]
    input_pdf = fetch_remote_pdf(remote_pdf_url)

    # Generate watermark text
    random_number = random.randint(0, 1000000000)
    timestamp = datetime.now().strftime("%m/%d/%Y %H:%M:%S")
    watermark_text = f"ORAL AS Licence Utilisateur: {random_number} | Opened on: {timestamp}"

    # Add watermark to the PDF
    watermarked_pdf = add_watermark_to_pdf(input_pdf, watermark_text)

    # Serve the watermarked PDF
    return send_file(
        watermarked_pdf,
        as_attachment=True,
        download_name=f"watermarked_{pdf_id}.pdf",
        mimetype="application/pdf"
    )

if __name__ == "__main__":
    app.run(debug=True)