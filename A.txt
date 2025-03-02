import streamlit as st
import pandas as pd

# Define departments and their subjects with predefined credits
departments = {
    "AI&DS":[
    {"name": "Introduction to Machine Learning", "credit": 3},
    {"name": "Internet and Web Programming", "credit": 3},
    {"name": "Distributed and Cloud Computing", "credit": 3},
    {"name": "Data Exploration and Visualization", "credit": 3},
    {"name": "Environment and Agriculture", "credit": 3},
    {"name": "Introduction to Machine Learning Laboratory", "credit": 1.5},
    {"name": "Internet and Web Programming Laboratory", "credit": 1.5},
    {"name": "Mini Project II", "credit": 2},
    {"name": "Career Skills Development I", "credit": 1},
    {"name": "Introduction to LaTeX", "credit": 1}
    ],

    "AI&ML": [
    {"name": "Fundamentals of Data Science", "credit": 3},
    {"name": "Natural Language Processing and its Techniques", "credit": 3},
    {"name": "Soft Computing Techniques", "credit": 3},
    {"name": "Theory of Computation", "credit": 3},
    {"name": "Environment and Agriculture", "credit": 3},
    {"name": "Data Science Laboratory", "credit": 1.5},
    {"name": "Natural Language Processing and its Techniques Laboratory", "credit": 1.5},
    {"name": "Mini Project II", "credit": 2},
    {"name": "Career Skills Development I", "credit": 1},
    {"name": "Introduction to LaTeX", "credit": 1}
    ],

    "CSE": [
    {"name": "Web Technology", "credit": 3},
    {"name": "Software Engineering", "credit": 3},
    {"name": "Theory of Computation", "credit": 3},
    {"name": "Cryptography and Network Security Principles", "credit": 3},
    {"name": "Environment and Agriculture", "credit": 3},
    {"name": "Web Technology Laboratory", "credit": 1.5},
    {"name": "Software Engineering Laboratory", "credit": 1.5},
    {"name": "Security Laboratory", "credit": 1.5},
    {"name": "Mini Project II", "credit": 2},
    {"name": "Career Skills Development I", "credit": 1},
    {"name": "Introduction to LaTeX", "credit": 1}
    ],

    "ECE": [
    {"name": "Digital Signal Processing", "credit": 3},
    {"name": "VLSI System Design", "credit": 3},
    {"name": "Computer Networks (IC)", "credit": 4},
    {"name": "Microprocessor and Microcontrollers (IC)", "credit": 4},
    {"name": "Embedded Systems (IC)", "credit": 4},
    {"name": "Foundations of Artificial Intelligence and Machine Learning (IC)", "credit": 4},
    {"name": "Mini Project II", "credit": 2},
    {"name": "Career Skills Development I", "credit": 1},
    {"name": "Intellectual Property Rights for Engineers", "credit": 1}
    ],
    
    "EEE": [
    {"name": "Control System Engineering", "credit": 3},
    {"name": "Electric Vehicles", "credit": 3},
    {"name": "Smart Grid and Renewable Energy Systems", "credit": 3},
    {"name": "Measurement and Instrumentation", "credit": 3},
    {"name": "High Voltage Engineering", "credit": 3},
    {"name": "3D Printing and Design", "credit": 3},
    {"name": "Control and Instrumentation Laboratory", "credit": 1.5},
    {"name": "Renewable Energy Laboratory", "credit": 1.5},
    {"name": "Mini Project II", "credit": 2},
    {"name": "Career Skills Development I", "credit": 1},
    {"name": "Intellectual Property Rights for Engineers", "credit": 1}
    ],

}

# Grade mapping
grade_values = {"O": 10, "A+": 9, "A": 8, "B+": 7, "B": 6, "C": 5}
grade_options = list(grade_values.keys())  # Allowed grades

# Initialize session state keys
if "selected_department" not in st.session_state:
    st.session_state["selected_department"] = None
if "current_page" not in st.session_state:
    st.session_state["current_page"] = "department_selection"

# Function to calculate GPA
def calculate_gpa(subjects):
    total_credits = sum(sub["credit"] for sub in subjects)
    total_score = sum(grade_values.get(sub["grade"], 0) * sub["credit"] for sub in subjects)
    
    if total_credits == 0:
        return None
    return total_score / total_credits

# Function to show department selection
def department_selection():
    st.markdown("<h1 style='text-align: center; color: #FF4B4B;'>🎓 CGPA Calculator</h1>", unsafe_allow_html=True)

    department = st.selectbox(
        "Select Your Department:", 
        list(departments.keys()), 
        key="department_selection"
    )

    if st.button("Proceed ➡️", use_container_width=True):
        st.session_state["selected_department"] = department
        st.session_state["current_page"] = "department_page"
        st.rerun()

# Function to show the department-specific GPA calculator
def department_page():
    if st.session_state["selected_department"] is None:
        st.session_state["current_page"] = "department_selection"
        st.rerun()

    st.markdown(f"<h2 style='text-align: center; color: #FFA500;'>📚 {st.session_state['selected_department']} - GPA Calculator</h2>", unsafe_allow_html=True)
    
    subjects = departments[st.session_state["selected_department"]]

    # Create DataFrame for table display
    df = pd.DataFrame({
        "Subject": [sub["name"] for sub in subjects],
        "Credits": [sub["credit"] for sub in subjects],
        "Grade": ["O"] * len(subjects),  # Default grade
    })

    # Editable Table for Grade Selection
    edited_df = st.data_editor(
        df, 
        column_config={
            "Grade": st.column_config.SelectboxColumn("Grade", options=grade_options),
        },
        use_container_width=True
    )

    # Apply the selected grades back to subjects
    for i, subject in enumerate(subjects):
        subject["grade"] = edited_df.loc[i, "Grade"]

    # Calculate GPA Button
    if st.button("Calculate GPA 🎯", use_container_width=True):
        gpa = calculate_gpa(subjects)
        if gpa is not None:
            st.success(f"✅ Your GPA is: **{gpa:.2f}** 🎉")

    # Back Button
    if st.button("⬅️ Back to Department Selection", use_container_width=True):
        st.session_state["current_page"] = "department_selection"
        st.rerun()

# Handle navigation
if st.session_state["current_page"] == "department_selection":
    department_selection()
elif st.session_state["current_page"] == "department_page":
    department_page()
