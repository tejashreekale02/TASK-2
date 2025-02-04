import pandas as pd
from fpdf import FPDF

# Load data from CSV
def load_data(file_path):
    """Load data from a CSV file."""
    return pd.read_csv(file_path)

# Analyze data
def analyze_data(df):
    """Calculate basic statistics for numeric columns."""
    analysis = {}
    for column in df.select_dtypes(include=['int64', 'float64']).columns:
        analysis[column] = {
            "Mean": df[column].mean(),
            "Median": df[column].median(),
            "Max": df[column].max(),
            "Min": df[column].min(),
        }
    return analysis

# Generate PDF report
def generate_pdf_report(df, analysis, output_file="report.pdf"):
    """Generate a PDF report with data and analysis."""
    pdf = FPDF()
    pdf.add_page()
    pdf.set_font("Arial", size=12)

    # Add a title
    pdf.cell(200, 10, txt="Data Analysis Report", ln=True, align="C")

    # Add the data table
    pdf.set_font("Arial", size=10)
    pdf.cell(200, 10, txt="Data Table", ln=True, align="L")
    pdf.ln(5)
    col_width = 40
    row_height = 10

    # Add headers
    for col in df.columns:
        pdf.cell(col_width, row_height, txt=col, border=1)
    pdf.ln(row_height)

    # Add rows
    for index, row in df.iterrows():
        for col in df.columns:
            pdf.cell(col_width, row_height, txt=str(row[col]), border=1)
        pdf.ln(row_height)

    # Add analysis results
    pdf.ln(10)
    pdf.set_font("Arial", size=12)
    pdf.cell(200, 10, txt="Analysis Results", ln=True, align="L")
    pdf.ln(5)
    pdf.set_font("Arial", size=10)

    for column, stats in analysis.items():
        pdf.cell(200, 10, txt=f"Statistics for {column}:", ln=True, align="L")
        for stat, value in stats.items():
            pdf.cell(200, 10, txt=f"{stat}: {value:.2f}", ln=True, align="L")
        pdf.ln(5)

    # Save the PDF
    pdf.output(output_file)
    print(f"PDF report generated: {output_file}")

# Main function
def main():
    # Load data
    file_path = "data.csv"
    df = load_data(file_path)

    # Analyze data
    analysis = analyze_data(df)

    # Generate PDF report
    generate_pdf_report(df, analysis)

if __name__ == "__main__":
    main()