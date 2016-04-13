# -*- coding: utf-8 -*-
# this file is released under public domain and you can use without limitations

#########################################################################
## This is a sample controller
## - index is the default action of any application
## - user is required for authentication and authorization
## - download is for downloading files uploaded in the db (does streaming)
## - api is an example of Hypermedia API support and access control
#########################################################################
from gluon.tools import Mail

def index():
   return dict(form=auth.login())


@auth.requires_login()
def admin_home():
    if not auth.user.is_admin:
        redirect(URL('user_home'))
    return dict()

def admin_complaints():
    try:
        if not auth.user.is_admin:
            redirect(URL('public_home'))
    except AttributeError:
            redirect(URL('public_home'))
    return dict()

def public_complaints():
    form=SQLFORM(db.complain,fields=['Description','Topic','Email','CurrentTime'])
    if form.process().accepted:
        response.flash='Complain Registered. ID is: '+ str(form.vars.id)
    elif form.errors:
        response.flash='Input has error'
    else:
        response.flash='Please Fill the form'
    return dict(form=form)

@auth.requires(auth.user and auth.user.is_admin)
def show_comp():
    ele_list = db().select(db.complain.ALL)
    return dict(ele_list=ele_list)

@auth.requires(auth.user and not auth.user.is_admin)
def user_home():
   return dict()

def aboutus():
    return dict()

def search_comp():
    form=FORM('Complain ID:',
    INPUT(_name='name', requires=IS_INT_IN_RANGE(1,1000)),INPUT(_type='submit'))
    if form.accepts(request,session):
        session.a=request.vars.name
        redirect(URL('search_comp1'))
    elif form.errors:
        response.flash = 'form has errors'
    else:
        response.flash = 'please fill the form'
    return dict(form=form)


def search_comp1():
    id=int(session.a)
    comp_list= db(db.complain.id==id).select(db.complain.ALL)
    return dict(comp_list=comp_list)

def sitewise_comp():
    ele_list = db().select(db.complain.Topic,distinct=True)
    return dict(ele_list=ele_list)

def sitewise_compd():
    t = request.args(0)
    s_id='%'+t+'%'
    comp_list= db(db.complain.Topic.like(s_id)).select(db.complain.ALL)
    return dict(comp_list=comp_list)

def search_solvcomp():
    comp_list= db(db.complain.Status=='Solved').select(db.complain.ALL)
    return dict(comp_list=comp_list)

def search_inpcomp():
    comp_list= db(db.complain.Status=='In-Process').select(db.complain.ALL)
    return dict(comp_list=comp_list)

@auth.requires(auth.user and auth.user.is_admin)
def change_compstatus():
    mid=request.args(0)
    db(db.complain.id ==mid).update(Status='Solved')
    session.a=mid
    ls= db(db.complain.id==mid).select(db.complain.Email)
    for e in ls:
        val=e.Email
    #print val    
    mail = Mail()
    mail.settings.server = 'smtp.gmail.com:587'
    mail.settings.sender = 'donotreply.tms1@gmail.com'
    mail.settings.tls = True
    mail.settings.login = 'donotreply.tms1@gmail.com:tushujain'
    msg='Your complaint with ID '+ mid +' has been resolved check on our portal'
    mail.send(val,'Tender Management System',msg)
    redirect(URL('search_inpcomp'))
    return dict(mid=mid)

def public_home():
    return dict()



#@auth.requires(auth.user and auth.user.is_admin)
@auth.requires_login()
def sell_an_item():
    uid=auth.user.id
    form=SQLFORM(db.item_info,fields=['Name','Description','minbidprice','enddate','CurrentTime','category','priority','picture'])
    form.vars.seller_id=uid
    if form.process().accepted:
        response.flash='Site Created. ID is: ',form.vars.id
        redirect(URL('show_activebids'))
    elif form.errors:
        response.flash='Input has error'
    else:
        response.flash='Please Fill the form'
    return dict(form=form)

def show_activebids():
    ele_list= db(db.item_info.Status=='Active').select(db.item_info.ALL)
    return dict(ele_list=ele_list)

@auth.requires(auth.user and auth.user.is_admin)
def adminshow_activebids():
    ele_list= db(db.item_info.Status=='Active').select(db.item_info.ALL)
    return dict(ele_list=ele_list)

@auth.requires(auth.user and auth.user.is_admin)
def show_pendingbids():
    ele_list= db(db.item_info.Status=='Confirmation Pending').select(db.item_info.ALL,orderby=db.item_info.priority)
    return dict(ele_list=ele_list)

@auth.requires(auth.user and auth.user.is_admin)
def active_bidding():
    sid=request.args(0)
    db(db.item_info.id ==sid).update(Status='Active')
    redirect(URL('adminshow_activebids'))
    return dict(mid=mid)

@auth.requires(auth.user and auth.user.is_admin)
def close_bidding():
    sid=request.args(0)
    ele_list=db(db.bidding.item_id==sid).select(db.bidding.ALL)
    max=0
    uid1=0
    iname=""
    for ed1 in ele_list:
        if ed1.bid_amt>max:
            max=ed1.bid_amt
            uid1=ed1.UserID
            iid=ed1.item_id
    mail1=""
    #iname=db(db.item_info.id ==iid).select(db.item_info.Name)
    #mail1=db(db.auth_user.id ==uid1).select(db.auth_user.email)
    db(db.item_info.id ==sid).update(sold_amt=max)
    db(db.item_info.id ==sid).update(buyers_id=uid1)
    db(db.item_info.id ==sid).update(Status='Bidding Closed')
    item_list=db(db.item_info.id==iid).select(db.item_info.ALL)
    for e in item_list:
        iname=e.Name
        sellid=e.seller_id
        pass
    user_list=db(db.auth_user.id==uid1).select(db.auth_user.ALL)
    for e in user_list:
        mail1=e.email
        pass
    user_list=db(db.auth_user.id==sellid).select(db.auth_user.ALL)
    for e in user_list:
        smail1=e.email
        pass
    #print max
    #print iname
    #print mail1
    #print uid1
    from gluon.tools import Mail
    mail = Mail()

    ## configure email
    mail = auth.settings.mailer
    mail.settings.server = 'smtp.gmail.com:587'
    mail.settings.sender = 'iiitbid@gmail.com'
    mail.settings.login = 'iiitbid@gmail.com:dkwnbkuobbjatsol'

    ## configure auth policy
    auth.settings.registration_requires_verification = False
    auth.settings.registration_requires_approval = False
    auth.settings.reset_password_requires_verification = True
    string="Congratulations!!!! You Have Bidded for "+iname+" at price "+str(max)+" and It is sold to you.Please Pay and collect the Item"
    mail.send(mail1,'Item Sold To You',string)
    string="Congratulations!!!! Your Item "+iname+" has been sold at price "+str(max)+" to "+mail1
    mail.send(smail1,'Your Item Sold!!!',string)
    redirect(URL('adminshow_closedbids'))
    return dict(mid=mid)

@auth.requires(auth.user and auth.user.is_admin)
def adminshow_closedbids():
    ele_list= db(db.item_info.Status=='Bidding Closed').select(db.item_info.ALL)
    return dict(ele_list=ele_list)


def show_progressbids():
    ele_list= db(db.item_info.Status=='Bidding Closed').select(db.item_info.ALL)
    return dict(ele_list=ele_list)

@auth.requires(auth.user and auth.user.is_admin)
def adminshow_tender():
    sid=request.args(0)
    tend_list= db(db.bidding.item_id==sid).select(db.bidding.ALL,orderby=db.bidding.bid_amt)
    return dict(tend_list=tend_list)

@auth.requires(auth.user and not auth.user.is_admin)
def apply_for_bid():
    sid=request.args(0)
    uid=auth.user.id
    ele=db(db.item_info.id==sid).select(db.item_info.ALL)
    for e in ele:
        siid=e.seller_id
        minbid=e.minbidprice
        pass
    user_list= db(db.auth_user.id==uid).select(db.auth_user.ALL)
    for u in user_list:
        name=u.first_name
    form=SQLFORM(db.bidding,fields=['Description','bid_amt','CurrentTime'])
    ele_list=db(db.item_info.id==sid).select(db.item_info.picture)
    #'RequiredDuration',
    form.vars.item_id = sid
    form.vars.UserID=uid
    form.vars.UName=name
    if siid != uid:
        if form.process().accepted:
            print form.vars.bid_amt
            print minbid
            #if minbid < int(form.vars.bid_amt):
            response.flash='Bid Accepted. ID is: ',form.vars.id
            redirect(URL('user_home'))
            #else:
             #   response.flash='Bid amount shoould be >  min bid amount'
                #return dict(form=form,ele_list=ele_list)
        elif form.errors:
            response.flash='Input has error'
        else:
            response.flash='Please Fill the form'
        return dict(form=form,ele_list=ele_list)
    else:
        response.flash='You are selling and u cannot bid'
        return dict(form=form,ele_list=ele_list)
@auth.requires_login()
def search_itembyid():
    form=FORM('Tender ID:',
    INPUT(_name='name', requires=IS_INT_IN_RANGE(1,1000)),INPUT(_type='submit'))
    if form.accepts(request,session):
        session.b=request.vars.name
        redirect(URL('search_itembyid1'))
    elif form.errors:
        response.flash = 'form has errors'
    else:
        response.flash = 'please fill the form'
    return dict(form=form)

@auth.requires_login()
def search_itembyid1():
    tid=int(session.b)
    uid=auth.user.id
    tend_list= db((db.bidding.id==tid) &(db.bidding.UserID==uid)).select(db.bidding.ALL)
    return dict(tend_list=tend_list)

@auth.requires(auth.user and auth.user.is_admin)
def approve_tender():
    tid=request.args(0)
    sid=request.args(1)
    db(db.bidding.item_id==sid).update(Status='Rejected')
    db(db.bidding.id ==tid).update(Status='Approved')
    db(db.item_info.id ==sid).update(Status='Work In Progress')
    tend_list= db(db.bidding.id==tid).select(db.bidding.ALL)
    for e in tend_list:
        val=e.UserID
        pass
    user_list=db(db.auth_user.id==val).select(db.auth_user.ALL)
    for e in user_list:
        val=e.email
        pass
    print val
    mail = Mail()
    mail.settings.server = 'smtp.gmail.com:587'
    mail.settings.sender = 'donotreply.tms1@gmail.com'
    mail.settings.tls = True
    mail.settings.login = 'donotreply.tms1@gmail.com:tushujain'
    msg='Congratulations!!! your Tender ID ' +tid +' has been approved. Contact respective office for further details'
    mail.send(val,'Approval of tender',msg)

    session.b=tid
    redirect(URL('adminshow_tender'))
    return dict(mid=mid)

def view_approvedtender():
    sid=request.args(0)
    ele_list= db(db.item_info.id==sid).select(db.item_info.ALL)
    for e in ele_list:
        ele_seller= db(db.auth_user.id==e.seller_id).select(db.auth_user.ALL)
        ele_buyer= db(db.auth_user.id==e.buyers_id).select(db.auth_user.ALL)
    return dict(ele_list=ele_list,ele_seller=ele_seller,ele_buyer=ele_buyer)

@auth.requires(auth.user and auth.user.is_admin)
def adminshow_userdet():
    user_list= db().select(db.auth_user.ALL)
    return dict(user_list=user_list)

@auth.requires(auth.user and auth.user.is_admin)
def adminshow_activity():
    uid=request.args(0)
    tend_list= db(db.bidding.UserID==uid).select(db.bidding.ALL)
    return dict(tend_list=tend_list)


@auth.requires(auth.user and auth.user.is_admin)
def adminsearch_userbyid():
    form=FORM('User ID:',
    INPUT(_name='name', requires=IS_INT_IN_RANGE(1,1000)),INPUT(_type='submit'))
    if form.accepts(request,session):
        session.c=request.vars.name
        redirect(URL('adminsearch_userbyid1'))
    elif form.errors:
        response.flash = 'Form has errors'
    else:
        response.flash = 'Please fill the Form'
    return dict(form=form)

@auth.requires(auth.user and auth.user.is_admin)
def adminsearch_userbyid1():
    id=int(session.c)
    user_list= db(db.auth_user.id==id).select(db.auth_user.ALL)
    return dict(user_list=user_list)

@auth.requires(auth.user and auth.user.is_admin)
def adminsearch_userbyname():
    form=FORM('User Name:',
    INPUT(_name='name'),INPUT(_type='submit'))
    if form.accepts(request,session):
        session.d=request.vars.name
        redirect(URL('adminsearch_userbyname1'))
    elif form.errors:
        response.flash = 'Form has errors'
    else:
        response.flash = 'Please fill the Form'
    return dict(form=form)

@auth.requires(auth.user and auth.user.is_admin)
def adminsearch_userbyname1():
    t=session.d
    u_id='%'+t+'%'
    user_list= db(db.auth_user.first_name.like(u_id)).select(db.auth_user.ALL)
    return dict(user_list=user_list)

@auth.requires_login()
def usershow_myitems():
    uid=auth.user.id
    tend_list= db(db.item_info.seller_id==uid).select(db.item_info.ALL)
    return dict(tend_list=tend_list)


@auth.requires_login()
def usershow_mybids():
    uid=auth.user.id
    tend_list= db(db.bidding.UserID==uid).select(db.bidding.ALL)
    return dict(tend_list=tend_list)


def user():
    """
    exposes:
    http://..../[app]/default/user/login
    http://..../[app]/default/user/logout
    http://..../[app]/default/user/register
    http://..../[app]/default/user/profile
    http://..../[app]/default/user/retrieve_password
    http://..../[app]/default/user/change_password
    http://..../[app]/default/user/reset_password
    http://..../[app]/default/user/manage_users (requires membership in
    use @auth.requires_login()
        @auth.requires_membership('group name')
        @auth.requires_permission('read','table name',record_id)
    to decorate functions that need access control
    """
    return dict(form=auth())


@cache.action()
def download():
    """
    allows downloading of uploaded files
    http://..../[app]/default/download/[filename]
    """
    return response.download(request, db)


def call():
    """
    exposes services. for example:
    http://..../[app]/default/call/jsonrpc
    decorate with @services.jsonrpc the functions to expose
    supports xml, json, xmlrpc, jsonrpc, amfrpc, rss, csv
    """
    return service()


@auth.requires_login() 
def api():
    """
    this is example of API with access control
    WEB2PY provides Hypermedia API (Collection+JSON) Experimental
    """
    from gluon.contrib.hypermedia import Collection
    rules = {
        '<tablename>': {'GET':{},'POST':{},'PUT':{},'DELETE':{}},
        }
    return Collection(db).process(request,response,rules)
