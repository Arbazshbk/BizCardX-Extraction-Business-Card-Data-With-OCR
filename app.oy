!pip install streamlit 
!pip install easyOCR 

import pandas as pd
import numpy as np
import streamlit as st
import easyocr
import cv2
import sqlite3
import time

# Connect to sqlite3 database
conn = sqlite3.connect('Business_card_info.db')
cur = conn.cursor()

# Create a table to store the business card information
cur.execute('''CREATE TABLE IF NOT EXISTS Business_card
              (id INT AUTO_INCREMENT PRIMARY KEY,
              name TEXT,
              position TEXT,
              address TEXT,
              pincode VARCHAR(25),
              phone VARCHAR(25),
              email TEXT,
              website TEXT,
              company TEXT
              )''')

# Using easyOCR for reading data
reader = easyocr.Reader(['en'])

# Set the page title and page icon
st.set_page_config(page_title= "BizCardX: Extracting Business Card Data with OCR", page_icon=':credit_card:', layout='wide')

# Title
st.title(":white[BizCardX: Extracting Business Card Data with OCR]")


# Background
st.markdown(
    """
    <style>
    .main {
    background-color: #5CDB95;
    }
    </style>
    """,
    unsafe_allow_html=True
    )

# Create a file uploader
file_upload = st.sidebar.file_uploader(":green[UPLOAD CARD IMAGE>>>:credit_card:]",
                               type=["jpg", "jpeg", "png", "tiff", "tif", "gif"])
st.sidebar.image("https://media.istockphoto.com/id/1006701810/video/identity-icon-animation.jpg?s=640x640&k=20&c=Fkn8E8fbyEVoCbCQZkyTvYNpsApscoF8ekuZAWIlQsI=", )


# Create a sidebar menu with options to Add, Show, Update business card information

data = ['Insert Data', 'Show Data', 'Edit Card Info', 'Delete Data']
choose = st.sidebar.radio("Select An Option", data)

if choose == 'Insert Data':
    if file_upload is not None:

        # Read the image using OpenCV
        image = cv2.imdecode(np.fromstring(file_upload.read(), np.uint8), 1)

        # Display the uploaded image
        st.image(image, caption='Uploaded Successfully', use_column_width=True)

        # Button to extract information from the image
        if st.button('Extract Data And Added'):
            bsc = reader.readtext(image, detail=0)
            text = "\n".join(bsc)

            # Insert the extracted information and image into the database
            sql_data = "INSERT INTO Business_card (name, position, address, pincode, phone, email, website, company) VALUES ( ?, ?, ?, ?, ?, ?, ?, ?)"
            values = (bsc[0], bsc[1], bsc[2], bsc[3], bsc[4], bsc[5], bsc[6], bsc[7])
            cur.execute(sql_data, values)
            conn.commit()
            with st.spinner("Wait for it...."):
                time.sleep(5)

            # Display message
            st.success(":blue[Data Inserted]")

elif choose == 'Show Data':

    # Display the stored business card information
    cur.execute("SELECT * FROM Business_card")
    result = cur.fetchall()
    df = pd.DataFrame(result,
                      columns=['id', 'name', 'position', 'address', 'pincode', 'phone', 'email', 'website', 'company'])
    st.write(df)
    st.snow()


elif choose == 'Edit Card Info':

    # Create a dropdown menu to select a business card to edit
    cur.execute("SELECT id, name FROM Business_card")
    result = cur.fetchall()
    business_cards = {}

    for row in result:
        business_cards[row[1]] = row[0]
    select_card_name = st.selectbox("Select Card To Edit", list(business_cards.keys()))

    # Get the current information for the selected business card
    cur.execute("SELECT * FROM Business_card WHERE name=?", (select_card_name,))
    result = cur.fetchone()

    # Get edited information
    name = st.text_input("Name", result[1])
    position = st.text_input("Position", result[2])
    address = st.text_input("Address", result[3])
    pincode = st.text_input("Pincode", result[4])
    phone = st.text_input("Phone", result[5])
    email = st.text_input("Email", result[6])
    website = st.text_input("Website", result[7])
    company = st.text_input("Company_Name", result[8])


    # Create a button to update the business card
    if st.button("Edit Card Data"):
        # Update the information for the selected business card in the database
        cur.execute(
            "UPDATE Business_card SET name=?, position=?, address=?, pincode=?, phone=?, email=?, website=?, company=? WHERE name=?",
            (name, position, address, pincode, phone, email, website, company, select_card_name))
        conn.commit()
        st.success("Card Data Updated")

if choose == 'Delete Data':

    # Create a dropdown menu to select a business card to delete
    cur.execute("SELECT id, name FROM Business_card")
    result = cur.fetchall()
    business_cards = {}

    for row in result:
        business_cards[row[1]] = row[0]
    select_card_name = st.selectbox("Select Card To Delete", list(business_cards.keys()))

    # Create a button to delete the selected business card
    if st.button("Delete Card"):
        # Delete the selected business card from the database
        cur.execute("DELETE FROM Business_card WHERE name=?", (select_card_name,))
        conn.commit()
        st.success("Card Data Deleted")
