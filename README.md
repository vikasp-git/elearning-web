# elearning-web
def logout(request):
    auth.logout(request)
    return render(request,'home.html')

def postsignup(request):

    name=request.POST.get('name')
    roll=request.POST.get('rollno')
    email=request.POST.get('email')
    passw=request.POST.get('pass')
    try:
        user=authe.create_user_with_email_and_password(email,passw)
        uid = user['localId']
        data={"name":name,"roll":roll}
        database.child("Users").child(uid).set(data)
    except:
        message="Unable to create account try again"
        return render(request,"signup.html",{"messg":message})
    return render(request,"home.html")



def create(request):

    return render(request,'upload_home.html')

def upload_save(request):
    dept = request.POST.get('dept')
    OverView =request.POST.get('overview')
    url = request.POST.get('url')
    title = request.POST.get('vtitle')
    data_json = {
        'bookOverview': OverView,
        'url': url
    }
    database.child(dept).child('Reading').child(title).set(data_json)
    return render(request,'Welcome.html')

def post_create(request):

    import time
    from datetime import datetime, timezone
    import pytz

    tz= pytz.timezone('Asia/Kolkata')
    time_now= datetime.now(timezone.utc).astimezone(tz)
    millis = int(time.mktime(time_now.timetuple()))
    print(("mili"+str(millis)))
    # dept = request.POST.get('dept')
    
    vtitle = request.POST.get('vtitle')
    OverView =request.POST.get('progress')
    url = request.POST.get('url')
    idtoken= request.session['uid']
    a = authe.get_account_info(idtoken)
    a = a['users']
    a = a[0]
    a = a['localId']
    dept = database.child("ClubDept").child(a).child("name").get()
    if request.POST.get('work'):
        Title = request.POST.get('work')
    else:
        all_courses = database.child(dept.val()).child("courses").get()
        courses = []
        try:
            for course in all_courses.each():
                courses.append(course.key())
        except :
            pass
        # print (";;;;;;;;;;;;;;;;;;;;",courses)
        count = request.POST.get('title')
