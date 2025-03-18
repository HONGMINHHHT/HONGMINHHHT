import streamlit as st
import pandas as pd
import json

# Khởi tạo danh sách lớp
if "classes" not in st.session_state:
    st.session_state.classes = {}

st.title("Grade Management System")

# Nhập tên lớp
class_name = st.text_input("Class Name:")
if st.button("Add Class"):
    if class_name and class_name not in st.session_state.classes:
        st.session_state.classes[class_name] = []
        st.success(f"Class {class_name} added successfully!")
    else:
        st.warning("Class already exists or invalid name!")

# Hiển thị danh sách lớp
if st.session_state.classes:
    selected_class = st.selectbox("Select Class:", list(st.session_state.classes.keys()))

    # Nhập thông tin học sinh
    st.subheader("Add Student")
    student_id = st.text_input("Student ID:")
    student_name = st.text_input("Student Name:")
    dob = st.text_input("Date of Birth:")
    
    if st.button("Add Student"):
        if student_id and student_name and dob:
            student_data = {"ID": student_id, "Name": student_name, "DOB": dob, "Scores": {}, "Final Grade": None}
            st.session_state.classes[selected_class].append(student_data)
            st.success("Student added successfully!")
        else:
            st.warning("Invalid input!")
    
    # Nhập điểm số
    st.subheader("Add Score")
    student_id_score = st.text_input("Enter Student ID:")
    subject = st.text_input("Subject:")
    score = st.number_input("Score:", min_value=0.0, max_value=10.0, step=0.1)
    
    if st.button("Add Score"):
        for student in st.session_state.classes[selected_class]:
            if student["ID"] == student_id_score:
                student["Scores"][subject] = score
                student["Final Grade"] = sum(student["Scores"].values()) / len(student["Scores"])
                st.success("Score added successfully!")
                break
        else:
            st.warning("Student not found!")
    
    # Hiển thị danh sách học sinh
    st.subheader("Class Data")
    df = pd.DataFrame(st.session_state.classes[selected_class])
    st.dataframe(df)
